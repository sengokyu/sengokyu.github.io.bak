---
date: '2018-07-12T12:08:47+09:00'
layout: post
published: true
qiita_article_id: 7247281b05e11aaa96b7
tags:
- Chrome
- firefox
- Angular
title: '[Angular] Angularアプリをブラウザのコンソールからデバッグする方法'
updated: '2018-07-12T12:18:04+09:00'

---
Angularのコンポーネントを、ブラウザのコンソールからちょっと覗いてみる方法です。  
  
AngularJS時代は、[angular.element($0).scope()](2015-01-29-AngularJSJavaScriptAngularJS.md)なんて方法でいけました。それのAngular時代版です。  
  
# 見たいエレメントを選ぶ  
  
インスペクタから見たいエレメントを選びます。  
  
![image.png](/assets/images/6133c83a-f28b-001d-6175-a3b410bf57a0.png)  
  
  
# コンソールへ`ng.probe`と入力  
  
次のように入力すると、コンポーネントオブジェクトがダンプできます。  
  
****  
```js:
ng.probe($0).componentInstance
```  
![image.png](/assets/images/7f7d739d-1104-10c9-9a13-4d7d6b4154bc.png)  
  
  
何かエラーが出たら、間違ったエレメントを選んだか、デバッグモードが無効になっていると思われます。  
