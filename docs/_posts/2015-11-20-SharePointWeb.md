---
date: '2015-11-20T11:28:19+09:00'
layout: post
published: true
qiita_article_id: 9b024387a0d127c2176a
tags:
- SharePoint
title: '[SharePoint] ページビューアーWebパーツが動かない'
updated: '2015-11-20T11:28:19+09:00'

---
# 問題 :dizzy_face:   
  
ページに「ページビューザー」Webパーツを追加し、URLを設定しているのに、まったく反映されないことがあります。  
  
何度設定しても、画面はこのままで変わりません。  
  
![pageviewr.JPG](/assets/images/b26bde9d-2877-26cc-620f-1b2ad651f1c6.jpeg)  
  
> コンテンツにリンクするには、ツールウィンドウを開き、[リンク]テキストボックスにURLを入力します。  
  
# 原因 :exclamation: :question:   
  
## (1) カスタムスクリプトの実行が許可されていない :question:   
  
Office365管理センターを開き、管理者カテゴリ内のSharePointを選択、設定をクリックすると下記の画面が出ます。  
  
![sp-customscript.JPG](/assets/images/78ac144d-d2d5-71c4-bbe5-7c95e3074826.jpeg)  
  
> カスタム スクリプト   
>  
> 個人用サイトとセルフサービス作成サイトでユーザーがカスタム スクリプトを実行できるかどうかを制御します。注意: この設定に対する変更が有効になるまで最大 24 時間かかる場合があります。  
>  
> 詳細については、http://go.microsoft.com/fwlink/?LinkId=397546 を参照してください  
  
カスタムスクリプトを実行できるようにしてください。  
  
## (2) 「ページの追加とカスタマイズ」権限がない :question:   
  
サイトの歯車アイコンからサイトの設定メニューを選択、ユーザと権限カテゴリにあるサイトの権限を開きます。  
  
リボンのアクセス許可レベルをクリックします。  
  
フルコントロールをクリックします。  
  
![sp-pagecustomize.JPG](/assets/images/671d4981-7ae2-7029-a8e7-f82ee518e060.jpeg)  
  
> ページの追加とカスタマイズ  -  HTML ページか Web パーツ ページを追加、変更、または削除し、Microsoft SharePoint Foundation 互換のエディターを使用して Web サイトを編集します。   
  
チェックがついていないければチェックをつけます。  
  
該当ユーザにフルコントロール権限を付与します。  
  
