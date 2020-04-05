---
date: '2019-06-05T12:52:41+09:00'
layout: post
published: true
qiita_article_id: 272bba52daace2e617ba
tags:
- テスト
- TypeScript
- Angular
- Zone.JS
title: '[Angular] テストレシピ - fakeAsyncの使い処'
updated: '2019-06-05T12:52:41+09:00'

---
# はじめに  
  
Angularのテストの中で、以下のような記述を見かけたことはないでしょうか？  
  
****  
```ts:
it('世界に挨拶します (async)', fakeAsync(() => {
    // テストコード
}));
```  
  
ここでは何が起きているのか？  
どんなときにこのように書けばよいのか？  
すでに解説記事がいくつもありますが、自分なりにまとめてみます。  
  
# fakeAsyncを使うと何が起きるの？  
  
[マニュアル](https://angular.io/api/core/testing) には、このように書かれています。  
  
> Wraps a function to be executed in the fakeAsync zone:  
>  
> -   microtasks are manually executed by calling flushMicrotasks(),  
> -   timers are synchronous, tick() simulates the asynchronous passage of time.  
  
簡単に訳すと、  
  
> fakeAsyncゾーンの中で実行するように関数をラップします。  
>   
> - microtaskは、flushMicrotasks()を呼ぶことで手動実行されます。  
> - タイマーは同期します。tick() を使うと、非同期な時間待ちをシミュレートできます。  
  
なんのことやらですね。  
  
## fakeAsyncゾーンとは？  
  
「fakeAsyncゾーン」というのは、「fakeAsync」という名前の「ゾーン」です。  
「ゾーン」というのは、実行コンテキストのようなものだそうです。  
  
ゾーンを解説するプレゼン動画 → https://www.youtube.com/watch?v=3IqtmUscE_U&t=150 [^1]  
  
[^1]: YouTubeは自動翻訳字幕出せるようになってた。素敵！  
  
何をしてくれるかというと、非同期コールバックをプロキシ化して、AOPできるようにしたようなものです。  
非同期コールバックが複数箇所にあったとして、呼び出し前に実行コンテキストを整えて、非同期コールバック間で情報を共有できるようにしてくれます。  
  
どのように実現しているかというと、`setTimeout`やらなんやらにモンキーパッチをあてているそうです。  
  
## microtaskとは？  
  
[Zone.jsのドキュメント](https://github.com/angular/zone.js/blob/master/doc/task.md)によると、microtaskというのは、Promise.Thenに渡される非同期コールバックのことです。  
  
つまり、`flushMicrotasks()`を呼ぶと、キューに溜まったPromise.Thenコールバックを全て実行してくれ（るんだと思い:sweat_smile: ）ます。  
  
ちなみに、非同期コールバックはmicrotaskとあわせて3つに分類されます。  
  
- MicroTask  
- EventTask  
- MacroTask  
  
## `tick()`は？  
  
`tick()`は簡単です。引数に指定したミリ秒時間待ってくれるだけです。  
  
  
# fakeAsync使うと美味しいの？  
  
おけ、テストをラップしてくれるのはわかった。  
どんなシーンで使うと嬉しいでしょうか。  
  
## 解決しないといけないPromiseがあるとき  
  
Promiseの解決結果がテストに影響するのであれば、fakeAsyncで囲みます。  
  
例えば、`Router.navigateByUrl()`を呼び出しているクラスをテストするときに使います。  
  
****  
```ts:
it('xxxしたら、oooへ遷移', fakeAsync(() => {
  // Given
  const location = TestBed.get(Location);

  // When
  instance.xxx(); // この中で、navigateByUrl を使っている

  flushMicrotasks(); // 溜まっているPromiseを強制解決

  // Then
  expect(location.path()).toBe('ooo'); // location.path() が変わっている
}));
```  
  
  
  
  
# 参考リンク  
  
- [Angular 公式ガイド](https://angular.io/guide/testing#async-test-with-fakeasync)  
- [AngularとZone.jsとテストの話](https://qiita.com/Quramy/items/83f4fbc6755309f78ad2)  
- [async awaitでキレイなコードが書けないと辛い気持ちになってたところ、zone.jsを使ったらいろいろと解決できそうなことが分かった](https://qiita.com/kodo_san/items/b31d8125cce438ff3706)  
- [Angular: Testing async stuff in the fakedAsync zone VS. providing custom schedulers](https://medium.com/@golbie/angular-testing-async-stuff-in-the-fakedasync-zone-vs-providing-custom-schedulers-27a7f83c7774)  
- [Angular fakeAsyncTest 使い方の纏め](https://qiita.com/Jialipassion/items/89bdaac33b3f2fbbffed)  
- [Implements Zones for JavaScript ](https://github.com/angular/zone.js/)  
  
