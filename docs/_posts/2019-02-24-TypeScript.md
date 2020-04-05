---
date: '2019-02-24T20:38:37+09:00'
layout: post
published: true
qiita_article_id: c1e857bbb84ad29bacdb
tags:
- TypeScript
title: TypeScriptでモジュール参照関係を解決できていない人（自分）のためのメモ
updated: '2019-02-24T20:38:37+09:00'

---
# はじめに  
  
Angularを使い始めて、「TypeScript簡単じゃん。他でも使うべ」と始めたら  
  
```
Cannot find name 'hogehoge'.
```  
  
とか  
  
```
Namespace 'hoge' has no exported member 'fuga'.
```  
  
とエラーが出てつまづきました。  
さらに他のプロジェクトを覗いてみたら、`import`無しのtsファイルだったりしてわけわからない。  
  
  
> 特にモジュールの参照や参照解決辺りで発生するエラーは、基本をしっかり理解できてないとトラブルシューティングするときかなりストレス感じます。  
[TypeScriptのモジュール解決のデモ](https://qiita.com/yoshiwatanabe/items/1d03ce4d8fb08f1800bc)  
  
と、まさにストレスを感じているわけでして、そんな人（自分）のためにまとめ直してみる次第です。  
  
  
# 混乱を招く2種類のモジュール  
  
TypeScriptで「モジュール」と言った時、意味としては2種類あります。  
  
* 内部モジュール = 名前空間  
* 外部モジュール = いわゆるモジュール  
  
数多ある解説記事やQ&Aがどちらを話題にしているのか気をつけないと、混乱に拍車がかかります。  
  
  
# 2種類のモジュールの見分け方  
  
以下のような記述が見えたら、内部モジュールを話題にしていると思ってよいです。  
  
* `module`  
* `namespace`  
* `/// <reference />`  
  
逆に上記がないときは、だいたい外部モジュールを話題にしていると思ってよいです。  
  
  
# 混ぜるな危険!? 内部モジュールと外部モジュール  
  
内部モジュールと外部モジュールを組み合わせて使う方法です。  
  
## 内部モジュールから外部モジュールを参照する  
  
残念ながらできません。  
  
  
  
****  
```ts:
module X {
  import { Foo } from 'foo'; 
}
```  
  
こんなプログラムを書くと、コンパイルエラーになります。  
  
```
TS1147: Import declarations in a namespace cannot reference a module.
```  
  
  
## 外部モジュールから内部モジュールを参照する  
  
ファイルの先頭にトリプルスラッシュ`///`コメントを入れます。  
`path`に内部モジュールを定義したファイル名を記述します。  
  
**foo.ts**  
```ts:foo.ts
module foo {
  export const X: number = 1234;
}
```  
  
**main.ts**  
```ts:main.ts
/// <reference path="./foo.ts" />

export class Baz {
  public output() {
    console.log(foo.X);
  }
}
```  
  
`///`はファイルの先頭にないと、ただのコメントになってしまいます。  
  
# 参考リンク  
  
* [次世代JavaScript系言語「TypeScript」の主要言語機能 (2/2)](https://www.atmarkit.co.jp/ait/articles/1302/01/news112_2.html)  
* [Typescript and typings: A mystery to solve](http://geekdirt.com/blog/typescript-and-typings/)  
* [Candy Cup Analogy](https://stackoverflow.com/a/30357635)  
