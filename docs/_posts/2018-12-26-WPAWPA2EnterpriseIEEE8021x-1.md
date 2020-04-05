---
date: '2018-12-26T09:56:30+09:00'
layout: post
published: true
qiita_article_id: 25f7562a595487f47c7b
tags:
- WiFi
- 802.1x
title: WPA/WPA2 Enterprise(IEEE 802.1x)環境構築 - その1 知識編
updated: '2018-12-26T09:56:30+09:00'

---
事前に共有した鍵を使った無線LAN(WPA Personal)ではなく、Active Directoryに登録されているユーザIDで接続できる無線LAN環境を構築します。  
  
# IEEE 802.1x用語  
  
自分の理解のために、環境構築時に必要そうな用語をまとめます。  
  
## サプリカント Supplicant  
  
無線LANのクライアントです。パソコンやスマホになります。  
  
## 認証機 Authenticator  
  
サプリカントが接続する機器です。無線LANのアクセスポイントです。  
  
## 認証サーバ Authentication Server  
  
認証機が接続する機器です。ここでユーザID/パスワードが認証されます。  
実際のところはRADIUSサーバです。これをActive Directoryドメインコントローラに任せます。  
  
## RADIUS - Remote Authentication Dial-In User Service  
  
認証と認可、さらに課金管理（アカウンティング）のプロトコルです。デフォルトでは、認証と認可はUDP 1812番ポート、アカウンティングはUDP 1813番ポートで通信します。  
  
自分が使うのは認証/認可だけなのでLDAPでもよいのですが、LDAP喋るWi-Fi APってないんですかね。  
  
## EAP - Extensible Authentication Protocol  
  
  
様々な認証方式をサポートするPPPです。  
  
  
## 認証方式  
  
サプリカントによって使える認証方式が違います。  
Windowsだと、以下のものが使えるようです。  
  
| Type | 認証方式 ||  
|:-:|:-:|:--|  
| 13  | EAP-TLS   | EAP-Transport Layer Security |  
| 25  | PEAP  | Protected EAP |  
| 26  | EAP-MS-CHAP v2  | EAP-Microsoft Challenge Handshake Authentication Protocol version 2 |  
  
EAP-TLSが最も強固ですが、クライアント証明書を運用したりしないといけないので面倒です。  
EAP-MS-CHAP v2が一番簡単だそうです。  
  
方式自体は、全部で何十個もあります。  
  
## PEAP - Protected EAP  
  
混沌としたEAP界に現れたCisco/Microsoft/RSA Security連合による認証方式です。  
しかし、versionが0から2まであって、混沌を生んでいます。  
  
PEAPの中で、さらに以下の認証方式を選ぶことができるようになっています。いよいよもってわかりません。  
  
* TLS  
* MS-CHAP v2  
  
上の項でEAP-MS-CHAP v2が使えるようなことを書きましたが、実際は使えません。  
PEAPの中でEAP-MS-CHAP v2を使います。  
  
## EAPoL - EAP over LAN  
  
レイヤ2上でEAPを通すプロトコルという理解で良いのかな？  
知らなくても大丈夫です。  
  
## WPA EnterpriseとWPA2 Enterprise  
  
暗号化方式/暗号化アルゴリズムが違います。  
  
|  | 暗号化方式 | アルゴリズム |  
|:-:|:-:|:-:|  
| WPA Enterprise  | TKIP  | RC4  |  
| WPA2 Enterprise  | CCMP  | AES  |  
  
RC4はやめて、AESを使っていきたいです。  
  
  
# 実際の環境構築に必要なもの  
  
## WPA/WPA2 Enterpriseに対応した無線LANクライアント  
  
PCのスペック表を眺めて、対応しているかどうか調べてください。  
USB等で後付けする際は、購入時の確認をお忘れなく。  
  
## WPA/WPA2 Enterpriseに対応した無線LANアクセスポイント  
  
自分は一番安価だった[アライド・テレシスのAT-MWS1750AP](https://www.allied-telesis.co.jp/products/list/wireless/mws_ap/catalog.html)を買いました。  
  
## RADIUSサーバ  
  
Windows Serverであれば、ネットワークポリシーサーバ(NPS)機能を有効にすると使えるようになります。  
  
## 信頼されている証明書  
  
無線LANクライアントから信頼(知っている認証局から発行)されている証明書が必要です。RADIUSサーバで使います。  
  
買うと結構なお値段がするので、オレオレ認証局を作って運用するのもありです。  
  
  
  
  
# 参考リンク  
  
  
  
* [IEEE802.1X認証とは、EAPとは](https://www.infraexpert.com/study/wireless14.html)  
* [EAPのはなし(2)](http://www.silex.jp/blog/wireless/2013/10/eap2.html)  
* https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd283091(v=ws.10)  
* https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-xp/bb457039(v=technet.10)  
* https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc759077(v=ws.10)  
* [RFC3748 Extensible Authentication Protocol (EAP)](https://tools.ietf.org/html/rfc3748)  
* [IETF Draft Microsoft EAP CHAP Extensions](https://tools.ietf.org/html/draft-kamath-pppext-eap-mschapv2-02)  
