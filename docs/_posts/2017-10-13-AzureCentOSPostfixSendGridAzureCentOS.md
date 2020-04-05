---
date: '2017-10-13T15:11:12+09:00'
layout: post
published: true
qiita_article_id: 2e55e1a5f6f3491c4c67
tags:
- CentOS
- Azure
- postfix
- SendGrid
title: '[Azure][CentOS][Postfix][SendGrid] Azureに借りたCentOSからメール送信してみる'
updated: '2017-10-16T14:16:13+09:00'

---
# ストーリー  
  
Azureに借りたサーバからメールが来なくなったなぁ？変だなぁ？  
と思いながらも放置プレイ。  
ふとメールログを見てみたらspamhausからはじかれてるじゃないですか。  
解除申請しようとするも、どうもAzureからのメール送信はMicrosoftのポリシーにより弾かれているご様子。  
  
検索すると、こんな記事が。。。  
[Azure 上にメールサーバー/SMTP サーバーを構築する場合の注意事項](https://blogs.technet.microsoft.com/jpaztech/2016/06/09/smtp-on-azurevm/)  
  
> Azure 上の VM から直接 SMTP 等を使ってメール送信することは、Azure プラットフォームとしてサポートをしておりません。  
  
うへー。だからといって、spamhausへ登録しなくても。。。 :disappointed:  
  
そんなわけで[SendGrid](https://sendgrid.org/)を使うことにしたのでした。  
  
# SendGridの導入  
  
毎月25､000通までなら無料で使えるそうです。  
  
Azure Marketplaceから購入します。  
  
参照 → [AzureのMarketplaceからSendGridを利用してみる](https://qiita.com/yotan/items/e89c6a89b0c2fbec4a4a)  
  
AzureをMicrosoftソリューションパートナーから導入していたりすると、Marketplaceからは購入できません。。。 :cry:  
  
# Postfixの設定  
  
SendGridにきちんとドキュメントが用意されているので、それをなぞります。  
  
参照 → [Integrate SendGrid with Postfix](https://sendgrid.com/docs/Integrate/Mail_Servers/postfix.html)  
  
## 設定ファイルの変更  
  
`/etc/postfix/main.cf`に以下を追記します。  
  
**/etc/postfix/main.cf**  
```text:/etc/postfix/main.cf
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
smtp_sasl_tls_security_options = noanonymous
smtp_tls_security_level = encrypt
header_size_limit = 4096000
relayhost = [smtp.sendgrid.net]:587
```  
  
## パスワード記載のファイルを作成  
  
新たに`/etc/postfix/sasl_passwd`ファイルを作成し、以下のような内容にします。  
  
**/etc/postfix/sasl_passwd**  
```text:/etc/postfix/sasl_passwd
[smtp.sendgrid.net]:587 ユーザ名:パスワード
```  
ユーザ名とパスワードは、Azure Portalから確認できます。  
  
パーミッションを変更してハッシュDB化します。  
  
```shell-session
$ sudo chmod 600 /etc/postfix/sasl_passwd
$ sudo postmap /etc/postfix/sasl_passwd
```  
  
## ライブラリの存在を確認  
  
```shell-session
$ rpm -q cyrus-sasl-plain
cyrus-sasl-plain-2.1.26-20.el7_2.x86_64
```  
  
もしなければ`yum`コマンドでインストールします。  
  
## Postfixを再起動  
  
```shell-session
$ sudo systemctl restart postfix
```  
  
## 送信確認  
  
`/bin/mail`コマンドや、`/usr/sbin/sendmail`コマンドを使って、メールが送信できるか確認します。  
  
お疲れ様でした。 :smile:   
  
# 2017/10/16追記 - SendGridの設定  
  
デフォルトでは、プレーンテキストのメールはmultipart/alternativeになり、開封追跡用の画像が埋め込まれます。  
不要な場合は無効にします。  
  
設定個所は以下にあります。  
  
* Mail Settings  
    * Plain Content → Activeにします。  
* Tracking  
    * Click Tracking → Inactiveにします。  
    * Open Tracking → Inactiveにします。  
