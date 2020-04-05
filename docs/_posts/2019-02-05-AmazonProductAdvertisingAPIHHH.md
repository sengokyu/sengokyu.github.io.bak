---
date: '2019-02-05T12:18:17+09:00'
layout: post
published: true
qiita_article_id: 1f7da02ec3f59d391982
tags:
- Amazon
- AmazonProductAdvertisingApi
- AmazonAssociate
title: Amazon Product Advertising APIの始め方^H^H^H始められなかった
updated: '2019-02-05T12:18:17+09:00'

---
Amazon Product Advertising APIの始め方です。  
  
1. Amazonアソシエイトにサインアップする  
2. Product Advertising APIにサインアップする  
3. 規約を読む  
4. [オプション] お試しする  
  
# Amazonアソシエイトにサインアップする  
  
使いたい地域（日本、US、イタリア等）によりサインアップ先が異なります。  
[Sign up as an Amazon Associate](https://docs.aws.amazon.com/AWSECommerceService/latest/DG/becomingAssociate.html)から、使いたい地域を選んでください。  
  
日本の場合は https://affiliate.amazon.co.jp/ です。  
  
# Product Advertising APIにサインアップする  
  
サインインしたら、上部メニューからProduct Advertising APIを選びます。  
  
![image.png](/assets/images/8d20e0f8-a1b9-0ab3-390f-d780b7cfca7a.png)  
  
利用を始めるをクリックします。  
  
![image.png](/assets/images/1908ee0a-4cc0-243f-f04f-90739046dd47.png)  
  
アクセスキーとシークレットキーが表示されますので、コピーして保存しておきます。  
あるいは認証情報をダウンローをクリックして、ファイルを保存しておきます。  
  
![image.png](/assets/images/e32854d2-4183-451d-88c0-f1d5308632d1.png)  
  
  
# 規約を読む  
  
[Amazonアソシエイト・プログラム運営規約](https://affiliate.amazon.co.jp/help/operating/agreement)を読みます。  
  
禁則事項がたくさんあるので気をつけないと。。。  
  
# [オプション] お試しする  
  
[ScratchPad](https://webservices.amazon.co.jp/scratchpad/index.html)にアクセスします。  
  
サイドバーから例えばItemSearchを選びます。  
  
Marketplaceを選びます。  
Access Key IDとSecret Access Keyを入力します。  
Run Requestをクリックします。  
  
結果が表示されるはずが、HTTP 503 You are submitting requsts too quickly. Please retry your requests at a slower rate. となりました。  
  
![image.png](/assets/images/f474ef6c-99c6-719f-d7ae-c3ac831c3d70.png)  
  
## 一晩待ってみる  
  
ダメ :cry:   
  
## 二晩待っても  
  
ダメ :sob:  
  
何を間違えたのだろう。。。  
