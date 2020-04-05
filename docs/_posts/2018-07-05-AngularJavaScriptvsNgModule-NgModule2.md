---
date: '2018-07-05T18:57:44+09:00'
layout: post
published: true
qiita_article_id: 52085ad12d85193d65d6
tags:
- JavaScript
- TypeScript
- Angular
title: '[Angular] JavaScriptモジュール vs. NgModule - @NgModuleとは何か (2)'
updated: '2018-07-06T09:24:21+09:00'

---
@NgModuleがなんなのかを知るにあたり、いくつか前提知識が必要となります。  
前回は[JavaScriptのモジュールについて](2018-07-05-AngularJavaScript-NgModule1.md)の勝手訳を書きました。  
今回は[Angular - JavaScript Modules vs. NgModules](https://angular.io/guide/ngmodule-vs-jsmodule)の勝手訳です。  
  
# 前段  
  
JavaScriptとAngularは、コードを整理するのにそれぞれ異なったやり方でモジュールを使います。Angularはその両方に依っています。  
  
  
# JavaScriptモジュール  
  
JavaScriptでは、モジュールはコードが書かれた個々のファイルです。  
  
モジュールの中身を使わせたいときは、こんなコードを書きます。  
  
****  
```js:
export class AppComponent { ... }
```  
  
そのモジュールを使いたいときは、こんなコードを書きます。  
  
****  
```js:
import { AppComponent } from './app.component'
```  
  
# NgModule  
  
NgModuleは`@NgModule`によって修飾されたクラスです。`@NgModule`のなかの`imports`配列は、そのモジュールが使いたい他のNgModuleを示します。これはJavaScriptのモジュールとは違います。  
`@NgModule`がついたクラスはそれぞれ個別のファイルに保存されていますが、JavaScriptのモジュールのように個別のファイルがNgModuleであることを意味するわけではありません。`@NgModule`とメタデータがあることでNgModuleになります。  
  
Angular CLIコマンドによって生成されたファイルは、JavaScriptモジュールとNgModule両方になっています。  
  
**app.module.js**  
```js:app.module.js
/*
これらはJavaScriptのインポートステートメントです。
Angularはこれらについて関知しません。
*/
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

/*
@NgModule修飾子が、AngularにこのクラスがNgModuleであることを示します。
*/
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [     /* 他のNgModuleをインポートしています。 */
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```  
  
NgModuleクラスとJavaScriptモジュールの違いを並べるとこんなところです。  
  
* NgModuleは[宣言可能なクラス](https://angular.io/guide/ngmodule-faq#q-declarable)にのみ束縛されます。宣言可能なクラスは、[Angularコンパイラ](https://angular.io/guide/ngmodule-faq#q-angular-compiler)にとって、唯一重要なクラスです。  
* JavaScriptモジュールのようにメンバークラスをひとつの巨大なファイルにまとめるのではなく、[@NgModule.declarations](https://angular.io/api/core/NgModule#declarations)リストにNgModuleクラスを列挙します。  
* NgModuleは、他からインポートしたか、自分自身である[宣言可能なクラス](https://angular.io/guide/ngmodule-faq#q-declarable)のみをエクスポートします。それ以外のクラスを宣言したりエクスポートしたりはしません。  
* JavaScriptモジュールと異なり、NgModuleは[@NgModule.providers](https://angular.io/api/core/NgModule#providers)リストにプロバイダを追加することにより、アプリケーション__全体__を拡張できます。  
  
