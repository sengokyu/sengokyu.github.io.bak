---
date: '2018-07-04T11:18:06+09:00'
layout: post
published: true
qiita_article_id: 1ba9ef760594fdfb50a0
tags:
- Ubuntu
- TypeScript
- Angular
- 写経
- VisualStudioCode
title: '[Angular] コンポーネントの作成 - UbuntuとVisual Studio Codeで始めるAngular (3)'
updated: '2018-07-11T18:47:32+09:00'

---
アプリケーションに簡単なタイトルを付けました。  
これから入力画面を作成します。  
  
# 入力画面コンポーネント  
  
`heroes`という名前のコンポーネントを生成します。  
  
```shell-session
ng generate component heroes
```  
  
`heroes`というフォルダの下にファイルが生成されます。  
  
```shell-session
CREATE src/app/heroes/heroes.component.css (0 bytes)
CREATE src/app/heroes/heroes.component.html (25 bytes)
CREATE src/app/heroes/heroes.component.spec.ts (628 bytes)
CREATE src/app/heroes/heroes.component.ts (269 bytes)
UPDATE src/app/app.module.ts (396 bytes)
```  
  
## コンポーネントクラス  
  
生成されたコンポーネントクラスです。  
  
**src/app/heroes/heroes.component.ts**  
```ts:src/app/heroes/heroes.component.ts
import { Component, OnInit } from "@angular/core";

///
/// `@Component` は、Angularのメタ情報を記述するための修飾関数です。
///
@Component({
  ///
  /// CSSでいうところの要素セレクタです。
  ///
  selector: "app-heroes",

  ///
  /// テンプレートファイルの場所を示します。
  ///
  templateUrl: "./heroes.component.html",

  ///
  /// コンポーネントのプライベートなCSSファイルの場所を示します。
  ///
  styleUrls: ["./heroes.component.css"]
})
///
/// クラスは常に `export` します。他から `import` するためです。
///
export class HeroesComponent implements OnInit {
  constructor() {}

  /// コンポーネントクラス生成時に呼び出されます。
  /// 初期化コードを記述します。
  ngOnInit() {}
}

```  
  
## ヒーロー名を表示してみる  
  
### プロパティを追加  
  
```heroes.component.ts
  ///
  /// ヒーロー名をプロパティとして定義してみます。
  ///
  hero: string = "ストーム";
```  
  
### テンプレートを更新  
  
```src/app/heroes/heroes.component.html
<!--
  自動生成されたコードを削除し、
  コンポーネントの `hero` プロパティを参照します。
-->
{{"{{"}}hero}}
```  
  
### アプリケーションシェルのテンプレートを更新  
  
定義したヒーロー名を参照するために`AppComponent`のテンプレートを更新します。  
  
**src/app/app.component.html**  
```html:src/app/app.component.html
<!--
  自動生成されたコードを消して、<h1>だけにします。
-->
<h1>{{"{{"}}title}}</h1>
<!--
  Heroesコンポーネントを参照します。
  ここで記述するのは、`selector` に記載した名前です。
-->
<app-heroes></app-heroes>
```  
  
# モデルクラスの作成  
  
名前だけじゃさみしいですね。名前以外も定義するためにクラスを作成します。  
  
雛形は`ng`コマンドで生成します。  
  
```shell-session
ng generate class Hero  
```  
  
中身はこんなんです。  
  
**src/app/hero.ts**  
```ts:src/app/hero.ts
export class Hero {
  ///
  /// ID番号です。
  ///
  id: number;

  ///
  /// 名前です。
  ///
  name: string;
}
```  
  
`heroes`コンポーネントで作成したクラスを使うようにします。  
  
**src/app/heroes/heroes.component.ts**  
```ts:src/app/heroes/heroes.component.ts
import { Component, OnInit } from "@angular/core";
///
/// 作成したクラスを `import` します。
import { Hero } from "../hero";

///
/// `@Component` は、Angularのメタ情報を記述するための修飾関数です。
///
@Component({
  ///
  /// CSSでいうところの要素セレクタです。
  ///
  selector: "app-heroes",

  ///
  /// テンプレートファイルの場所を示します。
  ///
  templateUrl: "./heroes.component.html",

  ///
  /// コンポーネントのプライベートなCSSファイルの場所を示します。
  ///
  styleUrls: ["./heroes.component.css"]
})
///
/// クラスは常に `export` します。他から `import` するためです。
///
export class HeroesComponent implements OnInit {
  constructor() {}

  ///
  /// ヒーローをHeroクラスとして定義します。
  hero: Hero = {
    id: 1,
    name: "ストーム"
  };

  /// コンポーネントクラス生成時に呼び出されます。
  /// 初期化コードを記述します。
  ngOnInit() {}
}
```  
  
## テンプレートを更新  
  
**src/app/heroes/heroes.component.html**  
```html:src/app/heroes/heroes.component.html
<!--
  自動生成されたコードを削除し、
  コンポーネントの `hero` プロパティを参照します。

  `hero`プロパティはクラスなので、クラスのプロパティを参照するようにします。
-->
<h2>{{"{{"}}hero.name}}の詳細</h2>
<div><span>id: </span>{{"{{"}}hero.id}}</div>
<div><span>名前: </span>{{"{{"}}hero.name}}</div>
```  
  
こんなんできました。  
  
![image.png](/assets/images/e6881c56-9ba1-6a64-3066-2a940fb657d1.png)  
  
## 入力欄を作成  
  
ヒーロー名を変更できるようにテキストボックスを作成します。  
  
データと表示、入力とデータが相互に反映されるように、双方向バインディングを使用します。  
  
## 双方向バインディング  
  
テンプレートを変更します。  
  
**src/app/heroes/heroes.component.html**  
```html:src/app/heroes/heroes.component.html
<!--
  自動生成されたコードを削除し、
  コンポーネントの `hero` プロパティを参照します。

  `hero`プロパティはクラスなので、クラスのプロパティを参照するようにします。
-->
<h2>{{"{{"}}hero.name}}の詳細</h2>
<div><span>id: </span>{{"{{"}}hero.id}}</div>
<div>
  <label>名前:
    <!--
      テキストボックスとhero.nameプロパティを双方向バインディングします。
      双方向バインディングするには、 `[(ngModel)]` と書きます。
    -->
    <input [(ngModel)]="hero.name" placeholder="名前">
  </label>
</div>
```  
  
## エラーまたは`FormsModule`の不在  
  
テンプレートを更新するとアプリケーションが停止します。  
ブラウザのコンソールを開くと、以下のようなメッセージが出力されています。  
  
```
Error: Template parse errors:
 Can't bind to 'ngModel' since it isn't a known property of 'input'. (
" 双方向バインディングするには、 `[(ngModel)]` と書きます。
 -->
 <input [ERROR ->][(ngModel)]="hero.name" placeholder="名前">
 </label>
 </div> "
): ng:///AppModule/HeroesComponent.html@14:11
```  
  
`ngModel`はデフォルトでは有効になっていないのです。  
  
  
## `FormsModule`をインポート  
  
`src/app/app.module.ts` ファイルを変更します。  
  
**src/app/app.module.ts**  
```ts:src/app/app.module.ts
import { BrowserModule } from "@angular/platform-browser";
import { NgModule } from "@angular/core";
///
/// ngModelを使えるようにするため
/// FormsModuleをインポートします。
import { FormsModule } from "@angular/forms";

import { AppComponent } from "./app.component";
import { HeroesComponent } from "./heroes/heroes.component";

///
/// Angularアプリケーションが動くため、どのようなパーツが必要なのかを
/// 教えてあげる必要があります。これをメタデータと呼びます。
///
/// いくつかのメタデータは `@Component` 修飾関数内に記述されます。
/// その他重要なメタデータは `@NgModule` 修飾関数内に記述されます。
///
/// `@NgModule` は、アプリケーショントップレベルの `AppModule`
/// クラスにあります。
@NgModule({
  declarations: [AppComponent, HeroesComponent],
  ///
  /// 外部モジュールを、ここの `imports` 配列内に記述します。
  imports: [
    BrowserModule,
    ///
    /// `FormsModule` を追記します。
    FormsModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {}

```  
  
## 使用するコンポーネントを宣言する必要性  
  
モジュールで使用するすべてのコンポーネントは、 `@NgModule` 内で選言しておく必要があります。  
  
**app.module.ts**  
```ts:app.module.ts
///
/// `ng`コマンドでの生成時に、自動的に追記されています。
import { HeroesComponent } from "./heroes/heroes.component";
```  
  
  
**app.module.ts**  
```ts:app.module.ts
@NgModule({
  ///
  /// 使用するすべてのコンポーネントを、ここで宣言する必要があります。
  ///
  /// `ng` コマンドで `HeroesComponent` を生成したので、自動的に
  /// 追記されています。
  declarations: [AppComponent, HeroesComponent],
```  
  
こんなんできました。  
  
![image.png](/assets/images/044b8296-f920-acdd-bec8-110c1f47ceed.png)  
  
  
