---
date: '2018-10-07T19:29:52+09:00'
layout: post
published: true
qiita_article_id: c5b155670c8b4ccd4789
tags:
- JavaScript
- テスト
- bottlejs
- InversifyJS
- awilix
title: '[JavaScript] Dependency Injectionコンテナどれにしよう？'
updated: '2018-10-09T18:24:37+09:00'

---
Azure FunctionsをJavaScriptで書いていました。  
テストを書くにあたり、やっぱり依存先クラスを自前でnewしてるのは辛いなと思い、DIコンテナを探すことにしました。  
  
# awilix  
  
https://www.npmjs.com/package/awilix  
  
こちら [nodejs, ES6 で dependency injection with awilix](https://qiita.com/sugiyasu-qr/items/3679d5041ee547ca28eb) で紹介されていました。  
  
  
コンテナを作り  
  
****  
```js:
const container = awilix.createContainer({
  injectionMode: awilix.InjectionMode.PROXY
})
```  
  
コンテナにクラスを登録し  
  
****  
```js:
container.register({
  userController: awilix.asClass(UserController)
})
```  
  
コンテナからインスタンスを取り出して使います。  
  
****  
```js:
var userController = container.resolve('userController');
```  
  
簡単です。  
  
依存関係を直接書かなくても解決してくれるのかな？いいですね。  
  
  
# InversifyJS  
  
https://github.com/inversify/InversifyJS  
  
こちら [InversifyJSで学ぶDI(Dependency Injection)](https://qiita.com/Quramy/items/e65ee58cf1fba589c81b) で紹介されていました。  
  
  
  
クラスを修飾し  
  
****  
```js:
inversify.decorate(inversify.injectable(), Katana);
inversify.decorate(inversify.injectable(), Shuriken);
inversify.decorate(inversify.injectable(), Ninja);
```  
  
依存関係を登録し（引数の0や1は、多分コンストラクタ引数の順番）  
  
****  
```js:
inversify.decorate(inversify.inject("Katana"), Ninja, 0);
inversify.decorate(inversify.inject("Shuriken"), Ninja, 1);
```  
  
コンテナを作成し  
  
****  
```js:
var container = new inversify.Container();
```  
  
型名とクラスを紐付け  
  
****  
```js:
container.bind("Ninja").to(Ninja);
container.bind("Katana").to(Katana);
container.bind("Shuriken").to(Shuriken);
```  
  
コンテナからインスタンスを取り出して使います。  
  
  
****  
```js:
var ninja = container.get("Ninja");
```  
  
だいぶ冗長ですね。安全のために仕方ないみたいです。  
TypeScriptだとデコレータがあるのでよい感じです。  
  
# bottlejs  
  
https://github.com/young-steveo/bottlejs  
  
こちら [bottlejsでDIを試す](https://qiita.com/jgs/items/551c6c992d538c1d35fa) で紹介されていました。  
  
コンテナを作成し  
  
****  
```js:
var bottle = new Bottle();
```  
  
コンテナに型名とクラスを登録し  
  
****  
```js:
bottle.service('Barley', Barley);
bottle.service('Hops', Hops);
bottle.service('Water', Water);
```  
  
コンテナに依存関係を登録し  
  
****  
```js:
bottle.service('Beer', Beer, 'Barley', 'Hops', 'Water');
```  
  
コンテナからインスタンスを取り出して使います。  
  
****  
```js:
var beer = bottle.container.Beer
```  
  
シンプルですね。  
コードサンプルがいい（笑）  
  
  
# その他  
  
npmjs探すといっぱいありますね。  
  
* [Aurelia](https://aurelia.io/) のDIコンテナ部分  
* [Deep Framework](https://github.com/MitocGroup/deep-framework) のDIコンテナ部分  
  
# 結論  
  
それなりのチームであればinversifyJS、少人数and/of小規模ならawilixかbottlejsがよさそうです。  
  
ところで、探してはみたけれど、Azure FunctionsでDIコンテナ使うのはごしたい感じ。  
