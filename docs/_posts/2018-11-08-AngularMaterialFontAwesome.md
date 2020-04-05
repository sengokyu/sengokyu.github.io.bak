---
date: '2018-11-08T14:59:17+09:00'
layout: post
published: true
qiita_article_id: 57261ad37e925b6c5637
tags:
- Angular
- FontAwesome
- MaterialDesign
title: Angular Material + Font Awesome使い方メモ
updated: '2018-11-08T14:59:38+09:00'

---
[Angular Material](https://material.angular.io/)  
と  
[Font Awesome](https://fontawesome.com/)  
を組み合わせたときの使い方メモです。  
  
いつものように、CDNからCSSを読みこみます。  
  
**index.html**  
```html:index.html
<link rel="stylesheet"
 href="https://use.fontawesome.com/releases/v5.5.0/css/all.css"
 integrity="sha384-B4dIYHKNBt8Bc12p+WXckhzcICo0wtJAoU8YZTY5qE0Id1GSseTk6S+L3BlXeVIU"
 crossorigin="anonymous">
```  
  
コンポーネントHTMLにはこのように書きます。  
  
****  
```html:
<mat-icon fontSet="fa" fontIcon="fa-address-book"></mat-icon>
```  
  
アイコンの名前はチートシートから参照します。  
[Font Awesomeチートシート](https://fontawesome.com/cheatsheet)  
  
  
