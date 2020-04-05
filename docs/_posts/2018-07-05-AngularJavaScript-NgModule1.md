---
date: '2018-07-05T18:10:15+09:00'
layout: post
published: true
qiita_article_id: a62cb8d75733b0321706
tags:
- JavaScript
- TypeScript
- Angular
title: '[Angular] JavaScriptのモジュール - @NgModuleとは何か (1)'
updated: '2018-07-05T18:10:15+09:00'

---
`@NgModule`がなんなのかを知るにあたり、いくつか前提知識が必要となります。  
最初はJavaScriptのモジュールです。  
  
[ES6 In Depth:Modules](https://hacks.mozilla.org/2015/08/es6-in-depth-modules/) の勝手訳です。  
  
# 前段  
  
Googleマップが現れる前は、「典型的なJavaScriptプログラムは1行のみ」というジョークがはやっておった。  
`<input onChange=`  
というやつじゃ。大抵はそれで事足りた。  
  
現代のJavaScriptプログラムはいくつものファイルやディレクトリにまたがる巨大なものになった。  
そんなとき、必要とされるのはモジュールシステムじゃろう。  
実際、JavaScriptにはモジュールシステムがある。いくつもな。。。  
ここで現れたES6のモジュール文法は、パーティに遅刻してきた輩じゃと感じるかもしれん。  
  
  
# モジュールの基礎  
  
ES6のモジュールは、JavaScriptプログラムを含んだファイルです。そこには`module`という単語はありません。普通にJavaScriptプログラムとして読めます。普通と違うところは、以下の2つです。  
  
* 自動的にstrictモードになります。  
* `export`と`import`が使えます。  
  
## `export`と`import`  
  
既定では、モジュール内で宣言されたすべてのものは、モジュールの中からだけ見えます。モジュールの外に公開したいときはエクスポート(export)します。エクスポートのやり方はいくつかあります。一番簡単なのは`export`キーワードを付けることです。  
  
**helloworld.js**  
```js:helloworld.js
export function helloWorld() {
  // 何か楽しいこと
}

export class TheWorld {
  // もっと楽しいこと
}

// この関数はエクスポートされません。
function workHard() {
}
```  
  
トップレベルの`function`、`class`、`var`、`let`、`const`に`export`を付けることができます。  
  
モジュールを書く上で知っていなくちゃいけないことは2つです。全体を即時関数に入れないこととコールバックにしないことです。  
  
エクスポートしたものを他のファイルから使いたいときはインポート(import)します。  
  
**demo.js**  
```js:demo.js
import { helloWorld } from 'helloworld.js'
```  
  
一度に複数個インポートすることもできます。  
  
**demo.js**  
```js:demo.js
import { helloWorld, TheWorld } from 'helloworld.js'
```  
  
`import`文は一番最初に実行されます。深さ優先で実行していきます。  
  
# エクスポートリスト  
  
エクスポートする別の書き方もあります。  
  
**helloworld.js**  
```js:helloworld.js
export { helloWorld, TheWorld }

function helloWorld() {}
class TheWorld {}
```  
  
エクスポートリストは先頭にある必要はありません。ファイルのどこに書いてもよいです。  
  
  
# 別名をつける  
  
インポートすると名前が衝突するとき、別名をつけることができます。  
  
**demo.js**  
```js:demo.js
import { Solaris as SolarisOS } from 'sun.js'
import { Solaris as SolarisMovie } from 'hollywood.js'
```  
  
エクスポートするほうで別名をつけることもできます。  
  
**ishihara.js**  
```js:ishihara.js
class Kuniko {}

export { Kuniko as Satomi }
```  
  
# Defaultエクスポート  
  
ES6モジュールはCommonJSやAMDとの相互運用性も考えられています。  
  
人気のlodashを使うとき、ES6では各関数をインポートします。  
  
**lodashdemo.js**  
```js:lodashdemo.js
import { each, map } from 'lodash'

each([3, 2, 1], x => console.log(x));
```  
  
けど、`each`と書かずに`_.each`と書くシーンもあるかもしれません。  
あるいは`_`を関数として扱いたいとか。  
そんなときは中括弧をつけずにこう書きます。  
  
****  
```js:
import _ from 'lodash'
```  
これは、`import { default as _ } from 'lodash'`を省略した書き方です。  
  
CommonJSやAMDのモジュールは、`default`としてエクスポートされます。  
  
# モジュールオブジェクト  
  
長くなりました。もう少し語ってないことがあります。`import *` です。  
  
****  
```js:
import * as cows from 'cows'
```  
  
ここで`cows`が`moo`をエクスポートしていれば、`cows.moo`として参照するようにします。  
  
  
# モジュールを集約  
  
複数ファイルから構成されるモジュールを、一箇所でエクスポートしたいことがあるかもしれません。  
  
****  
```js:
// yokohamaをインポートして、Shumaiを再エクスポートします。
export { Shumai } from 'yokohama'
// fukushimaをインポートして、MomoとSakuranboを再エクスポートします。
export { Momo, Sakuranbo } from 'fukushima'
// hokkaidoをインポートして、すべてを再エクスポートします。
export * from 'hokkaido'
```  
  
# 実際のところインポートって何してるのさ  
  
何もしてない、、、かもしれません。  
  
大雑把に言うとこんな感じです。  
  
1. パース - ソースを呼んで文法チェックします。  
2. ロード - モジュールを再帰的に読み込みます。このあたりはまだ標準化されてないとか。  
3. リンク - 読み込んだ各モジュールを適切なスコープに配置したりなんだりします。もしここでエクスポートされていないのにインポートしているものがあれば、エラーになります。  
4. 実行 - 読み込み終わったモジュールを実行します。もしここで`import`文があっても何も起きません。  
