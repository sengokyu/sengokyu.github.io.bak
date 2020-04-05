---
date: '2015-11-11T18:24:05+09:00'
layout: post
published: true
qiita_article_id: e5bba050ef1e0246463b
tags:
- CentOS
- firewalld
title: '[firewalld][CentOS][RHEL][RedHat][Linux] レンサバ(CentOS7)のファイアウォール設定。not iptables,
  but firewalld'
updated: '2016-08-09T09:10:50+09:00'

---
  
# 概要  
  
iptablesばっちこーいな気分でいたら、CentOS 7(RHEL7)からはfirewalldというものを使うそうです。新しくて面倒気分ですが、たぶんほとんどのシーンでは今までより簡単に使えそうです。  
  
信頼するIPアドレスは「trusted」ゾーンへ、悪さするIPアドレスは「block」ゾーンへ、「public」ゾーンには公開するサービスだけを登録、こんな感じでいけます。  
  
# 環境  
  
 - CentOS 7.1  
  
****  
```text:
$ cat /etc/redhat-release
CentOS Linux release 7.1.1503 (Core)
```  
  
# はじめにfirewalldのこと  
  
firewalldを設定する上で肝要なのは、ゾーンという考え方です。「Home」とか「Public」という名前を見るとWindowsみたいですが、ちょいと違います。  
  
その他にもいくつか新しい用語があります。  
  
## ゾーンとは  
  
あるネットワーク接続の信頼レベルの定義です。 ← 直訳すぎてわかんないですね。  
  
まず、ネットワーク接続を分類します。分類方法は、どのネットワークインターフェースを使っているか、差出元IPアドレスがなんであるか、ポート番号がなんであるかです。そのネットワーク接続をゾーンという単位でまとめます。  
  
初期状態で以下のようなゾーンが存在します。  
  
 - drop 有無を言わさずパケットを落とします。IDENT要求なんかはこのゾーンに入れてあげるといいかも。  
 - block アクセス全拒否したいIPアドレスは、このゾーンに入れてあげます。  
 - public  
 - external  
 - dmz  
 - work  
 - home  
 - internal  
 - trusted アクセス全許可するIPアドレスは、このゾーンに入れてあげます。  
  
\# dmzゾーンって、頭痛が痛いですね。。。  
  
## サービスとは  
  
公開するプロトコル/ポート番号/宛先IPアドレスをまとめたものです。  
  
/usr/lib/firewalld/services ディレクトリの下にxmlファイルとして設定されています。  
  
## リッチルールとは  
  
iptablesに設定するルールの簡易版です。読みやすい(?)代わりに機能が少ないです。  
  
次のオプションを指定できます。  
  
 - 差出元IPアドレス  
 - 宛先IPアドレス  
 - firewalldが提供するサービス、ポート番号、プロトコル、ICMPタイプ、IPマスカレードするかどうか、フォワードするかどうか  
 - ロギング  
 - アクション（action、reject、drop）  
  
## ロックダウンとは  
  
ファイアウォールがユーザやアプリケーションから変更できないようにします。  
ホワイトリスト形式で変更を許すユーザやアプリケーションを指定できます。  
  
# 設定方針  
  
こんな感じの方針で設定します。  
  
 - trustedゾーンに接続を許すIPアドレスを登録  
 - 公開するネットワークインターフェースをpublicゾーンにする  
 - publicゾーンから不要なサービスを削除  
 - publicゾーンに公開するサービスを追加  
  
# 設定手順  
  
## firewalldを有効化して起動  
  
何はともあれ起動します。  
  
```
# systemctl enable firewalld
ln -s '/usr/lib/systemd/system/firewalld.service' '/etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service'
ln -s '/usr/lib/systemd/system/firewalld.service' '/etc/systemd/system/basic.target.wants/firewalld.service'
# systemctl start firewalld
```  
  
## trustedゾーンに接続を許すIPアドレスを登録  
  
```
# firewall-cmd --zone=trusted --add-source=xxx.xxx.xxx.xxx
success
# firewall-offline-cmd --zone=trusted --add-source=xxx.xxx.xxx.xxx
success
```  
  
## インターフェースをpublicゾーンへ追加  
  
```
# firewall-cmd --zone=public --add-interface=eth0
success
# firewall-offline-cmd --zone=public --add-interface=eth0
success
```  
  
## publicゾーンから不要なサービスを削除  
  
```
# firewall-cmd --zone=public --remove-service=dhcpv6-client
success
# firewall-cmd --zone=public --remove-service=ssh
success
# firewall-cmd --permanent --zone=public --remove-service=dhcpv6-client
success
# firewall-cmd --permanent --zone=public --remove-service=ssh
success
```  
  
## publicゾーンに公開するサービスを追加  
  
httpとhttpsを公開するようにしました。  
  
```
# firewall-cmd --zone=public --add-service=http
success
# firewall-cmd --zone=public --add-service=https
success
# firewall-cmd --permanent --zone=public --add-service=http
success
# firewall-cmd --permanent --zone=public --add-service=https
success
```  
  
# 設定の確認  
  
## 使用しているゾーンの確認  
  
インターフェースやソースアドレスを登録したゾーンを一覧表示します。  
  
```
# firewall-cmd --get-active-zones
public
  interfaces: eth0
trusted
  sources: xxx.xxx.xxx.xxx
```  
  
## 公開しているサービスの確認  
  
ソーンに追加されているサービスを一覧表示します。  
  
```
# firewall-cmd --zone=public --list-services
# firewall-cmd --zone=trusted --list-services
```  
  
ゾーン名を指定しないと、デフォルトゾーン（`firewall-cmd --get-default-zone`で確認できます）が対象となります。  
  
  
  
