---
'created_at: ': '2016-10-19T10:56:06+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Ubuntu
      versions: []
    - name: skype
      versions: []
    - name: Office365
      versions: []
    - name: Lync
      versions: []
  state: 0
title: "[Ubuntu][Skype for Business][Lync] Ubuntu 16.04\u304B\u3089Skype for Business\u3078\
  \u63A5\u7D9A\u3059\u308B"
'updated_at: ': '2019-09-21T16:19:03+09:00'

---
# 概要  
  
Ubuntu 16.04でSkype for Businessへ接続できるようにします。  
  
こちらで紹介されている内容のなぞりです。  
http://chrisjrob.com/2015/09/02/linux-microsoft-skype-for-business-lync-2013-client/  
  
# ステップ 1 pidginプラグインのインストール  
  
最初にプラグインをインストールします。  
  
```
% sudo apt-get install pidgin-sipe
```  
  
インストールが終わったら、Pidginを再起動します。  
  
# ステップ 2 アカウントを追加  
  
次のような内容でアカウントを追加します。  
  
Basicタブ  
  
| 設定項目 | 設定内容 |  
|:-:|:-:|  
| Protocol  | Office Communicator[^1] |  
| Username | Skype for BusinessへログインするIDでです。たぶんにメールアドレスです。 |  
| Login | 空でOKです。 |  
| Password | Skype for Businessへログインするパスワードです。 |  
  
[^1]: この選択肢が表示されないときは、Pidginを再起動できていません。  
  
Advancedタブ  
  
| 設定項目 | 設定内容 |  
|:-:|:-:|  
| Server | 空でOKです。 |  
| Connection type | Auto |  
| User Agent | UCCAPI/15.0.4420.1017 OC/15.0.4420.1017[^2] |  
| Authentication scheme | TLS-SDK |  
  
[^2]: なんでかわからないけど必要らしいです。  
  
# おしまい  
  
もし接続できないときは、バージョンを確認してみてください。  
  
自分はこのバージョンで接続できました。  
  
Package: pidgin  
Version: 1:2.10.12-0ubuntu5.1  
  
Package: pidgin-sipe  
Version: 1.20.1-1  
  
  
\# それにしても、なぜMicrosoftはLyncという名前を捨ててSkype for Businessにしたのか。。。わかりづらいったらありゃしない。  
