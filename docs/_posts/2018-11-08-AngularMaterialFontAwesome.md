---
'created_at: ': '2018-11-08T14:59:17+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Angular
      versions: []
    - name: FontAwesome
      versions: []
    - name: MaterialDesign
      versions: []
  state: 0
title: "Angular Material + Font Awesome\u4F7F\u3044\u65B9\u30E1\u30E2"
'updated_at: ': '2018-11-08T14:59:38+09:00'

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
  
  
