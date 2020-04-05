---
date: '2018-11-10T12:34:40+09:00'
layout: post
published: true
qiita_article_id: 1883e0bf2a1a17bf4810
tags:
- TypeScript
- Angular
title: '[Angular] アプリケーションを複数の機能モジュールに分割する  - @NgModuleとは何か (5)'
updated: '2019-10-17T16:22:19+09:00'

---
`@NgModule`とは何かを探るシリーズ第5回です。  
今回は、アプリケーションを複数のモジュールに分割する方法です。  
  
  
今までの記事です。  
  
- [JavaScriptのモジュール - @NgModuleとは何か (1)](2018-07-05-AngularJavaScript-NgModule1.md)  
- [JavaScriptモジュール vs. NgModule - @NgModuleとは何か (2)](2018-07-05-AngularJavaScriptvsNgModule-NgModule2.md)  
- [ブートストラッピング - @NgModuleとは何か (3)](2018-07-06-Angular-NgModule3.md)  
- [よく使われるモジュール達 - @NgModuleとは何か (4)](2018-07-16-Angular-NgModule4.md)  
  
# ルートモジュールと機能モジュール  
  
アプリケーションを起動するのがルートモジュールです。ルートモジュールの中に全ての機能を詰め込むこともできますが、適宜問題領域に応じて分割したほうが、アプリケーションを整理＆＆理解しやすいかもしれません。  
そうして分割した先のモジュールを機能モジュールといいます。実装上は違いないです。  
  
  
# 機能モジュールの作成  
  
ここでは、現在日付を表示するコンポーネントを含むモジュールを作成してみます。  
  
すでにアプリケーションがあるとして、`ng`コマンドで作成します。  
  
  
****  
```shell-session:
ng generate module MyFeature
```  
  
ファイルがひとつ生成されます。  
  
- `src/app/my-feature/my-feature.module.ts`  
  
ちなみに`--flat`オプションを付けるとディレクトリが作成されません。  
  
- `src/app/my-feature.module.ts`  
  
  
**src/app/my-feature/my-feature.module.ts**  
```ts:src/app/my-feature/my-feature.module.ts
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

@NgModule({
  declarations: [],
  imports: [
    CommonModule
  ]
})
export class MyFeatureModule { }
```  
  
この状態では、アプリケーションにはなにも影響ありません。  
宣言可能なクラスをこのモジュールに追加していきます。  
  
  
## モジュールにコンポーネントを追加  
  
`ng`コマンドで作成します。スラッシュで区切って、頭にモジュール名を付けます。付けないとルートモジュールが更新されます。  
  
****  
```shell-session:
ng generate component my-feature/CurrentDate
```  
  
ファイルが4つ生成されました。  
  
- `src/app/my-feature/current-date/current-date.component.scss`  
- `src/app/my-feature/current-date/current-date.component.html`  
- `src/app/my-feature/current-date/current-date.component.spec.ts`  
- `src/app/my-feature/current-date/current-date.component.ts`  
  
モジュールファイル `src/app/my-feature/my-feature.module.ts` も更新されています。  
  
ちなみに `--flat`オプションをつけて、`ng`コマンドを実行すると、ディレクトリ階層が一つ上がって以下のようになります。  
  
- `src/app/my-feature/current-date.component.css`  
- `src/app/my-feature/current-date.component.html`  
- `src/app/my-feature/current-date.component.spec.ts`  
- `src/app/my-feature/current-date.component.ts`  
  
## コンポーネントをエクスポート  
  
コンポーネントを自モジュール外で使えるようにするためエクスポートします。  
  
```my-feature.module.ts
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { CurrentDateComponent } from './current-date/current-date.component';

@NgModule({
  declarations: [CurrentDateComponent],
  imports: [
    CommonModule
  ],
  exports: [CurrentDateComponent] /* exports配列に追加 */
})
export class MyFeatureModule { }
```  
  
## ルートモジュールでモジュールをインポート  
  
Appモジュールファイルを変更します。  
  
```app.module.ts
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';
import { MyFeatureModule } from './my-feature/my-feature.module';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    MyFeatureModule /* imports配列に追加 */
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```  
  
## いつものようにコンポーネントを使う  
  
Appテンプレートへ追記します。  
  
```app.component.html
<app-current-date></app-current-date>
```  
  
## 実行します  
  
とりあえずデフォルトの内容が表示されました。  
  
![image.png](/assets/images/c00e43c5-fcb0-6aa2-52f2-9e7c3c78e6d9.png)  
  
あとはいつものように`current-date.component.*`を変更して機能を実装すればよいです。内容は大したことないので割愛します。  
