---
date: '2018-07-04T09:33:22+09:00'
layout: post
published: true
qiita_article_id: 919b3759f9f2419c4bb9
tags:
- Ubuntu
- TypeScript
- Angular
- 写経
- VisualStudioCode
title: '[Angular] アプリの新規作成 - UbuntuとVisual Studio Codeで始めるAngular (2)'
updated: '2018-07-11T18:47:11+09:00'

---
# アプリの新規作成  
  
適当なディレクトリで、以下のコマンドを入力します。  
  
```shell-session
ng new angular-tour-of-heroes
```  
  
以下のようにファイルが作成されます。  
  
```shell-session
CREATE angular-tour-of-heroes/README.md (1036 bytes)
CREATE angular-tour-of-heroes/angular.json (3692 bytes)
CREATE angular-tour-of-heroes/package.json (1326 bytes)
CREATE angular-tour-of-heroes/tsconfig.json (384 bytes)
CREATE angular-tour-of-heroes/tslint.json (2805 bytes)
CREATE angular-tour-of-heroes/.editorconfig (245 bytes)
CREATE angular-tour-of-heroes/.gitignore (503 bytes)
CREATE angular-tour-of-heroes/src/environments/environment.prod.ts (51 bytes)
CREATE angular-tour-of-heroes/src/environments/environment.ts (631 bytes)
CREATE angular-tour-of-heroes/src/favicon.ico (5430 bytes)
CREATE angular-tour-of-heroes/src/index.html (306 bytes)
CREATE angular-tour-of-heroes/src/main.ts (370 bytes)
CREATE angular-tour-of-heroes/src/polyfills.ts (3194 bytes)
CREATE angular-tour-of-heroes/src/test.ts (642 bytes)
CREATE angular-tour-of-heroes/src/assets/.gitkeep (0 bytes)
CREATE angular-tour-of-heroes/src/styles.css (80 bytes)
CREATE angular-tour-of-heroes/src/browserslist (375 bytes)
CREATE angular-tour-of-heroes/src/karma.conf.js (964 bytes)
CREATE angular-tour-of-heroes/src/tsconfig.app.json (194 bytes)
CREATE angular-tour-of-heroes/src/tsconfig.spec.json (282 bytes)
CREATE angular-tour-of-heroes/src/tslint.json (314 bytes)
CREATE angular-tour-of-heroes/src/app/app.module.ts (314 bytes)
CREATE angular-tour-of-heroes/src/app/app.component.css (0 bytes)
CREATE angular-tour-of-heroes/src/app/app.component.html (1141 bytes)
CREATE angular-tour-of-heroes/src/app/app.component.spec.ts (1005 bytes)
CREATE angular-tour-of-heroes/src/app/app.component.ts (207 bytes)
CREATE angular-tour-of-heroes/e2e/protractor.conf.js (752 bytes)
CREATE angular-tour-of-heroes/e2e/src/app.e2e-spec.ts (318 bytes)
CREATE angular-tour-of-heroes/e2e/src/app.po.ts (208 bytes)
CREATE angular-tour-of-heroes/e2e/tsconfig.e2e.json (213 bytes)

```  
  
`git init`も`npm install`もやってくれます。便利 :smile:   
  
  
作成したディレクトリに移動して、開発サーバを起動します。  
  
```shell-session
cd angular-tour-of-heroes
ng serve
```  
ブラウザで http://localhost:4200/ にアクセスします。  
`ng serve`に`--open`オプションを付けると、デフォルトブラウザで開いてくれます。  
  
![image.png](/assets/images/e542bb77-ed4d-657c-0f16-caff0657cac9.png)  
  
## Angularコンポーネント  
  
ブラウザに表示されているページは *アプリケーションシェル* です。  
シェルは、 `AppComponent` という名前のコンポーネントによって制御されています。  
*コンポーネント* とは、Angularアプリケーションの基礎となる部品です。  
コンポーネントは、データを画面に表示し、ユーザ入力を受け取り、入力に応じてなんらかのアクションを起こします。  
  
`src/app`フォルダ内に、`AppComponent`を構成する3ファイルがあります。  
  
1. `app.component.ts` — TypeScriptで書かれたコンポーネントのクラスです。  
2. `app.component.html` — HTMLで書かれた、コンポーネントのテンプレートです。  
3. `app.component.css` — コンポーネントのプライベートなスタイルです。  
  
  
  
# アプリケーションタイトルを変更  
  
`src/app/app.component.ts`ファイルを開いて、`title`プロパティを変更します。  
  
  
**src/app/app.component.ts**  
```ts:src/app/app.component.ts

import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  // `title` プロパティを変更します。
  // `title` プロパティはテンプレートである `app.component.html` 内で参照されています。
  title = 'Tour of Heroes';
}

```  
  
テンプレートも変更します。  
  
**src/app/app.component.html**  
```html:src/app/app.component.html
<!--
  自動生成されたコードを消して、<h1>だけにします。
-->
<h1>{{"{{"}}title}}</h1>
```  
  
空のCSSファイル `src/app/app.component.css` が生成されているので、アプリケーション全体に適用するスタイルを記述します。  
  
こんなんできました。  
  
![image.png](/assets/images/cd0f909c-3b44-625a-0839-31d7a8461051.png)  
