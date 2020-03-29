---
'created_at: ': '2018-07-16T19:14:18+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: TypeScript
      versions: []
    - name: Angular
      versions: []
  state: 0
title: "[Angular] \u3088\u304F\u4F7F\u308F\u308C\u308B\u30E2\u30B8\u30E5\u30FC\u30EB\
  \u9054 - @NgModule\u3068\u306F\u4F55\u304B (4)"
'updated_at: ': '2018-07-16T19:14:18+09:00'

---
`@NgModule`とは何かを知るシリーズ4回目です。  
Angular付属のよく使うモジュールの紹介です。  
  
今までの記事です。  
  
- [JavaScriptのモジュール - @NgModuleとは何か (1)](2018-07-05-AngularJavaScript-NgModule1.md)  
- [JavaScriptモジュール vs. NgModule - @NgModuleとは何か (2)](2018-07-05-AngularJavaScriptvsNgModule-NgModule2.md)  
- [ブートストラッピング - @NgModuleとは何か (3)](2018-07-06-Angular-NgModule3.md)  
  
# `BrowserModule`  
  
アプリケーションをブラウザ内で実行するのに必要です。  
ほぼほぼ必須ですかね。  
  
  
**使い方**  
```ts:使い方
import { BrowserModule } from "@angular/platform-browser";
```  
  
# `CommonModule`  
  
`NgIf`とか`NgFor`とかを使うのに必要です。  
`BrowserModule`読み込めば入ってます。  
  
**使い方**  
```ts:使い方
import { CommonModule } from "@angular/common";
```  
  
  
# FormsModule  
  
`input`系のタグで`NgModel`を使うのに必要です。  
  
**使い方**  
```ts:使い方
import { FormsModule } from "@angular/forms";
```  
  
# `ReactiveFormsModiule`  
  
`NgModel`ではなく、新しい`FormGroup`と`FormControl`を使うのに必要です。  
  
**使い方**  
```ts:使い方
import { ReactiveFormsModule } from "@angular/forms";
```  
  
# `RouterModule`  
  
SPA(Single Page Application)を作る際に使います。  
  
**使い方**  
```ts:使い方
import { RouterModule } from "@angular/router";
```  
  
# `HttpClientModule`  
  
サーバとHTTPでやり取りするの使います。  
  
**使い方**  
```ts:使い方
import { HttpClientModule } from "@angular/common/http";
```  
