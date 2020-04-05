---
date: '2017-05-23T17:59:20+09:00'
layout: post
published: true
qiita_article_id: 9dd2af723d791f0da4fb
tags:
- Linux
- CentOS
- oVirt
title: '[CentOS][Linux] oVirt 4.1で始める楽々仮想マシン管理'
updated: '2019-09-20T11:59:34+09:00'

---
# はじめに  
  
小規模ながらもプライベートクラウドの運用なんてことをしますと、ユーザ（社員）にもっと手軽に使ってもらいたいという欲求が出てきます。  
  
- 仮想マシンのデプロイを簡単にしたい  
- 案件ごとにネットワークを分離したい  
- 教える手間なくやりたい  
  
CloudStack、OpenStackなんてのがあるらしいのですが、どうも難しそうなのでoVirtを試してみることにしました。  
  
# 序章  
  
## oVirtを始めるのに用意するもの  
  
以下のようなものを用意しないといけないです。  
  
- oVirt Engineとそれを動かすマシン [^1]  
- oVirt Engineクライアント（Webブラウザ、JavaScript速くないとつらいので、Chromeおすすめ）  
- 仮想マシンホスト（OSは、oVirt Node、Fedora、CentOSが選べます）  
- ストレージとネットワーク  
  
[^1]: oVirtで立てた仮想マシンの上にoVirt Engineをインストールしたいなぁなんて考えていたのですけれど、鶏と卵でしたね :stuck_out_tongue_winking_eye:  
  
順番にやっていきます。  
  
## oVirt Engineを動かすマシン  
  
CentOS 7をインストールしました。  
  
DNSがちゃんと構成されていないと、何やら具合が悪いという話です。  
  
  
## oVirt Engineのインストール  
  
http://www.ovirt.org/download/ に書いてある手順に従いました。  
  
レポジトリの設定をインストールし、ovirt-engineをインストールします。  
  
```shell-session
# yum install http://resources.ovirt.org/pub/yum-repo/ovirt-release41.rpm
# yum install ovirt-engine
```  
  
oVirt Engineを構成するために、以下のようにコマンドを実行します。  
  
```shell-session
# engine-setup
```  
  
構成が終わると、Webブラウザから接続できるようになります。  
  
## 仮想マシンホスト  
  
[oVirt Node](http://www.ovirt.org/node/)、Fedora、CentOSが使えます。自分はCentOS 7を使うことにしました。  
  
### oVirtレポジトリのインストール  
  
OSのインストールが終わったら、レポジトリの設定をインストールします。  
  
```shell-session
# yum install http://resources.ovirt.org/pub/yum-repo/ovirt-release41.rpm
```  
  
### firewallの構成  
  
oVirtは複数のポートを使用するそうです。  
  
*eno1* インターフェースを通るパケットは全許可にしました。[^3]  
  
[^3]: どうもfirewallも自動で設定される様子なので、この手順は不要かもしれません。  
  
```shell-session
# firewall-cmd --permanent --zone=trusted --add-interface=eno1
```  
  
# oVirt Engineへ接続  
  
Webブラウザを立ち上げて、oVirt Engineが動くマシンへ接続します。  
  
オレオレ証明書なので、セキュリティの警告が出ますが、そのあたりはよしなに。  
  
日本語表示もできるみたいです。いいねぇ。  
  
![ovirt-01.png](/assets/images/6c8970db-5ed6-949d-2304-e1dd64f98212.png)  
  
管理ポータルへログインします。  
  
![ovirt-02.png](/assets/images/d44d3028-67b0-54df-9646-8c679c749cd6.png)  
  
こんなん表示されました。  
  
## 仮想マシンホストを追加  
  
データセンター > *Default* > クラスター > *Default* > ホストと辿り、［新規作成］をクリックします。  
  
ホストの名前、ホストのアドレス、rootユーザのパスワード[^2]を入力します。  
  
ちなみにoVirt Node OSの場合は、手動追加しなくても、自動で発見追加されるそうです。すごいね。  
  
[^2]: ssh公開鍵で接続できるはずなのですが、パスワードを入力しないと作成できませんでした。  
  
仮想ホストを追加すると、必要なソフトウェアやらなんやら一式設定されます。楽ちんすぎ。  
  
## ストレージを追加  
  
以下のドメイン機能を持ったストレージを追加できます。  
  
- Data 仮想マシンを置くところです。  
- ISO ISOイメージを置くところです。インストール時に使います。  
- Export マイグレーションやらなんやらで、OVFを吐き出したりする先として使います。  
  
このうち、最初のDataドメインは必須のものになります。  
  
### Dataドメインを追加  
  
既存のNFS共有をDataドメインとして追加しました。  
  
NFS共有にはホストから接続できればよく、エンジンからは接続できなくてもいいみたいです。  
  
共有の中身は空っぽである必要があります。  
  
### NFS共有の設定  
  
NFS共有は次のようになっている必要があるようです。  
  
 - uid: 36  
 - gid: 36  
 - パーミッション: 0755  
  
### NFS以外のストレージ  
  
NFS以外には、ローカルのファイルシステム、GlusterFS、iSCSI、ファイバチャネルが使えます。  
  
ローカルのファイルシステムを使うのが簡単そうなところですが、データセンター全体の設定を変えるか、新しいデータセンターを作る必要があります。  
  
## ISOドメインを追加  
  
ISOイメージを置くために、ISOドメインを追加しました。  
  
エンジンからISOイメージをアップロードするときは、エンジンからアクセスできる場所になっている必要があります。  
  
# ISOイメージファイルをアップロード  
  
ISOイメージファイルをISOドメインへアップロードします。アップロードには`engine-iso-uploader`というコマンドを使います。エンジンをインストールしたマシンに入っています。  
  
## 注意事項  
  
ISOドメインがNFSの場合、`engine-iso-uploader`コマンドを動かすマシンにNFSマウントできる必要があります。  
  
仮想ホスト上のNFS共有を使う場合、iptablesで塞がれていないか確認してください。  
  
  
## アップロード手順  
  
まずストレージ名を確認します。  
  
```shell-session
# engine-iso-uploader list
```  
  
確認したストレージに対してファイルをアップロードします。  
  
```shell-session
# engine-iso-uploader -i [ストレージ名] [ISOファイル名]...
```  
  
  
## ゲスト用のISOファイルをアップロード  
  
ゲスト用エージェントが入ったISOファイルをアップロードします。  
  
```shell-session
# yum install ovirt-guest-tools-iso
```  
  
/usr/share/ovirt-guest-tools-isoにあるisoファイルをアップロードします。  
  
```shell-session
# cd /usr/share/ovirt-guest-tools-iso
# engine-iso-uploader -i [ストレージ名] upload oVirt-toolsSetup_4.1-3.fc24.iso
```  
  
  
## Windows用のドライバをアップロード  
  
Windows用の仮想デバイスドライバもアップロードしておきます。  
  
```shell-session
# yum install virtio-win
```  
  
/usr/share/virtio-winにあるファイルをアップロードします。｀  
  
```shell-session
# cd /usr/share/virtio-win
# engine-iso-uploader -i [ストレージ名] upload virtio-win-0.1.126.iso virtio-win-0.1.126_amd64.vfd virtio-win-0.1.126_x86.vfd


