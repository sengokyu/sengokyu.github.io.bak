---
date: '2015-01-29T12:05:07+09:00'
layout: post
published: true
qiita_article_id: 02029aad29cd7f5744bb
tags:
- Angular
title: '[AngularJS][JavaScript] AngularJSアプリをブラウザのコンソールからデバッグする方法'
updated: '2016-08-08T13:37:09+09:00'

---
angularjsで定義したスコープの中身をちょっと覗いてみる方法です。  
  
すでに同記事多数かもしれません。お目汚し失礼。  
  
***  
  
こんな感じでスコープを覗けます。  
  
**console**  
```javascript:console
angular.element(document.querySelector('タグ名')).scope()
```  
  
class="ng-scope"が付いているので、こんな風に.ng-scopeクラスで探すほうが簡単かもしれません。何番目のスコープを取得するのかは、添え字で指定します。  
  
**console**  
```javascript:console
angular.element(document.querySelectorAll('.ng-scope')[0]).scope()
```  
  
  
__2015/2/4 追記__  
  
もっと素早いやりかたを教えていただきました。Thanks @miyabisun !  
  
Chrome/Firefoxであれば、デベロッパーツールから選んだ要素が```$0```に格納されますので、```angular.element``` の引数に ```$0``` を渡せばよいです。  
  
画面から選んで・・・・・・  
  
![インスペクタ](/assets/images/f8485c3e-7db5-041a-2ede-058e570abbf0.png)  
  
普通に引数に渡します。  
  
**console**  
```js:console
angular.element($0).scope()
```  
  
![コンソール](/assets/images/a39a0680-5861-9a9d-6ee0-94841c10ff4a.png)  
  
  
jQueryが入っていれば、もっと素早くいけるそうです。  
  
**console**  
```js:console
$($0).scope()
```  
  
  
***  
  
controller-as記法であれば、scope()の「コントローラ名」プロパティを参照するとコントローラが入っています。  
  
**console**  
```javascript:console
angular.element(document.querySelector('タグ名')).scope().コントーラ名
```  
  
__2015/7/16 追記__  
コンソールを使うのがだるくなったら、ブラウザ拡張をインストールすると楽ちんです。  
記事書きました → [AngularJSアプリ開発を楽にするブラウザ拡張「ng-inspector」](http://qiita.com/sengoku/items/47abe2a3e8ae3d532f35)  
  
