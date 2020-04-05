---
date: '2014-11-17T22:35:16+09:00'
layout: post
published: true
qiita_article_id: 8cb3e2be0e252aa419d7
tags:
- Angular
title: AngularJSのドキュメントを読む前に知っておきたいことメモ
updated: '2019-03-06T10:35:48+09:00'

---
# このメモの内容  
  
チュートリアルも読んだし、ばりばりAngularJSプログラム書いちゃうぜ！とui bootstrapのドキュメントを読んでけつまづきました。  
ドキュメント中に使われている単語がわからんのです（promiseって何よ！とか）。  
そんな単語を将来の為にメモしていきます。  
  
# 単語リスト  
  
## promise  
  
promise/deferredパターンでいうところのpromiseオブジェクト（へ？）。thenというメソッドを持っています。thenメソッドは第1引数に成功時の処理、第2引数に失敗時の処理を渡します。  
  
promiseが渡ってくるときは、こんな感じで使います。  
  
****  
```javascript:
somepromise.then(function() {
  /* 成功時の処理 */
}, function() {
  /* 失敗時の処理 */
});
```  
  
promise/deferredパターンは非同期処理を順番に実行するときに便利だとか（？）  
  
## controller-as  
  
ng-controller属性にコントローラ名を書いていると思います。そのコントローラに別名（？）を付けるやり方です。  
  
チュートリアルに書かれているやり方：  
  
****  
```html:
<ANY ng-controller="SomeCtrl">
  {{"{{"}}someModel}}
</ANY>
```  
  
controller-as記法を使ったやり方：  
  
****  
```html:
<ANY ng-contoller="SomeCtrl as some">
  {{"{{"}}some.someModel}}
</ANY>
```  
  
## service  
  
サービスオブジェクト。アプリケーション全体で参照したいものをいれておくのに便利。  
  
coreという名前のサービスを定義：  
  
****  
```javascript:
angular.module('myApp', []).factory('core', function () {
  var coreServiceInstance = {
    contextPath: "/myApp"
  };

  return coreServiceInstance;
});
```  
  
コントローラでcoreサービスを使用：  
  
****  
```javascript:
angular.module('myApp').controller('MyCtrl', [
  'core',
  function (core) {
    console.log('Context Path is :' + core.contextPath);
  }
]);
```  
  
## directive  
  
（2014/12/18追記）  
  
HTMLの中に書いてきたng-controllerやng-appやng-includeなんてのは、全部directiveです。  
  
angularjsは、directiveをがっちゃんこがっちゃんことHTMLの中に組み込んでいくスタイルなのですな。  
  
  
## スコープ  
  
変数のスコープ（グローバルとかローカルとかいいますね）と大体同じ意味です。  
  
文脈（コンテキスト）と表現したりもします。  
  
AngularJSで何かを参照するときは、そのスコープにあるものを参照します。  
  
サザエさんで例えると、スコープとしては磯野、フグ田、波野があり、波平やカツオを参照したいときは磯野スコープを見ていないといけないということです。同様に、タラちゃんはフグ田スコープ、イクラちゃんは波野スコープを見ないと存在しないです。  
  
スコープには親子関係があり、最初の親は`$rootScope`ただ一つです。  
残りのスコープは、すべて`$rootScope`から辿られる子供になります。  
  
ただ、グローバルスコープみたいに、どこでも参照できるものはないです。  
  
ディレクティブを作ったりしていると、「親スコープのxxxを参照する」なんてシーンに出くわすことがあります。  
  
サザエさんで例えると、磯野とフグ田は親子関係にあり、フグ田サザエの実体は磯野にあり、フグ田サザエは磯野サザエを参照しているということになります。  
