---
date: '2018-07-06T11:29:33+09:00'
layout: post
published: true
qiita_article_id: 4416ecafddac7ddeac53
tags:
- JavaScript
- TypeScript
- Angular
title: '[Angular] ブートストラップ - @NgModuleとは何か (3)'
updated: '2018-07-06T11:29:33+09:00'

---
`@NgModule`とはなんなのかを知るシリーズ3回目です。  
今回は[Bootstrapping](https://angular.io/guide/bootstrapping)の勝手訳です。  
  
今回の記事を理解するのに、以下の知識を前提としています。  
[JavaScriptのモジュール - @NgModuleとは何か (1)](2018-07-05-AngularJavaScript-NgModule1.md)  
[JavaScriptモジュール vs. NgModule - @NgModuleとは何か (2)](2018-07-05-AngularJavaScriptvsNgModule-NgModule2.md)  
  
  
# 前段  
  
NgModuleは、アプリケーションの各部位がどのように協調するかを記述しています。全てのアプリケーションには、常に最低ひとつのrootモジュールがあります。rootモジュールがアプリケーションを起動するブートストラップです。慣例により、これを`AppModule`と名付けます。  
  
Angular CLIを使ってアプリケーションを新規作成すると、デフォルトでは下記のようなファイルができあがります。[^1]  
  
[^1]: Angular CLI 6.0.6で生成  
  
**app.module.ts**  
```ts:app.module.ts
/*
JavaScriptモジュールをインポートしています。
*/
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

/*
@NgModule修飾子がついたAppModuleクラスです。
*/
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```  
  
JavaScriptのインポートに続いて、`@NgModule`がついた`AppModule`クラスがあります。  
  
`@NgModule`修飾子の存在が、クラスをNgModuleとして認識させます。`@NgModule`内のメタデータが、アプリケーションがどのようにコンパイルして起動されるかを示します。  
  
* `declarations` — このアプリケーションのたったひとつのコンポーネントです。  
* `imports` — `BrowserModule`をインポートしています。BrowserModuleは、DOMレンダリング、サニタイズ、locationといったブラウザに特化したサービスを持ちます。  
* `providers` — サービスプロバイダです。  
* `bootstrap` — Angularが生成するrootコンポーネントです。index.htmlウェブページに挿入されます。  
  
CLIによって自動生成されたコンポーネントが、`declarations`と`bootstrap`に列挙されています。  
  
# `declarations`配列  
  
`declarations`配列は、Angularにモジュールの支配下にあるコンポーネントがなんであるかを教えます。新しいコンポーネントを作成したら、`declarations`に追加しないといけません。  
  
`declarations`に記述せずにコンポーネントを使おうとすると、エラーメッセージが出力されます。  
  
`declarations`配列には、宣言可能なクラスのみを列挙できます。宣言可能なクラスとは、コンポーネント、ディレクティブ、パイプです。宣言可能なクラスが属することができるのは、一つのモジュールだけです。ひとつの宣言可能なクラスを異なるモジュールに追加するとエラーとなります。  
  
宣言可能なクラスは、そのモジュール内からのみ見えます。他のモジュールからは見えません。他のモジュールから見えるようにするには、そのモジュールからエクスポートし、他のモジュールでインポートします。  
  
# @NgModuleでディレクティブを使う  
  
ディレクティブ、コンポーネント、パイプを使うには、いくつかやることがあります。  
  
1. それを書いたファイルからエクスポートする。  
2. 適切なモジュールファイルでインポートする。  
3. `@NgModule`の`declarations`に追加する。  
  
順番に見ていきます。  
  
## クラスをエクスポート  
  
`@Directive`を付けたクラスファイルを作成し、エクスポートします。  
  
**src/app/item.directive.ts**  
```ts:src/app/item.directive.ts
import { Directive } from '@angular/core';

@Directive({
  selector: '[appItem]'
})
export class ItemDirective {
// code goes here
  constructor() { }

}
```  
  
## クラスをインポート  
  
NgModuleがあるファイルにインポートします。  
  
**src/app/app.module.ts**  
```ts:src/app/app.module.ts
import { ItemDirective } from './item.directive.ts'
```  
  
## `declarations`に追加  
  
`@NgModule`修飾子の`declarations`配列に追加します。  
  
**src/app/app.module.ts**  
```ts:src/app/app.module.ts
  declarations: [
    AppComponent,
    ItemDirective
  ],
```  
  
# `imports`配列  
  
このモジュール内のコンポーネントから参照されるコンポーネント、ディレクティブ、パイプをエクスポートしているモジュールを列挙します。今回のケースでは、`AppComponent`が`BrowserModule`のコンポーネント、ディレクティブ、パイプを参照しています。  
  
  
# `provider`配列  
  
アプリケーションが必要とするサービスを列挙します。ここで列挙されたサービスは、アプリケーション全体から使うことができます。詳しくは[Providers](https://angular.io/guide/providers)へ。  
  
  
# `bootstrap`配列  
  
アプリケーションはroot `AppModule`をブートストラップして起動します。ブートストラッププロセスは`bootstrap`配列に列挙されたコンポーネントを作成し、ブラウザのDOMに挿入します。  
  
各ブートストラップコンポーネントは、そのコンポーネント自身が持つコンポーネントツリーの根元となります。  
  
複数のコンポーネントツリーを配置することもできますが、ほとんどのアプリケーションはただひとつのコンポーネントツリーしか持たず、単一のコンポーネントをブートストラップします。  
  
  
  
  
