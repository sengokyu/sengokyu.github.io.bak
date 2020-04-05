---
date: '2018-11-12T18:57:37+09:00'
layout: post
published: true
qiita_article_id: 35a73dfb4bd76ed5e5c1
tags:
- CSS
- scss
- Angular
- MaterialDesign
title: '[Angular] Angular Materialを使うためのSCSS入門'
updated: '2018-11-12T18:57:37+09:00'

---
Angular Materialでテーマを作りたいと思い立ちました。  
SCSS全くわからないので、ちょっとわかるwwくらいまでがんばります。  
  
公式サイト https://sass-lang.com/  
  
# 文法  
  
SCSSの文法は、CSSを拡張した形になっています。  
CSSそのままでも、SCSSとして解釈されます。  
  
# 変数  
  
SCSSでは、変数を宣言して参照することができます。  
  
**宣言と参照の仕方**  
```scss:宣言と参照の仕方
.myclass {
  $mywidth: 100%;
  width: $mywidth;
}
```  
  
頭に`$`を付けて、値と`:`で区切ります。  
  
## 変数のスコープ  
  
変数のスコープは、その宣言を含む `{}` の内側に限定されます。  
  
`{}`の外側で宣言するとグローバルなスコープになります。  
  
`{}`の内側でも`!global`を付けるとグローバルなスコープになります。  
  
**グローバルスコープ**  
```scss:グローバルスコープ
$myglobal: blue;

.myclass {
  $alsoglobal: lightblue !global;
}
```  
  
  
## 変数の型  
  
| 型 | 説明 | 例 |  
|:--|:--|:--|  
| 数値 | 数値です。  | `1.5`, `8px`  |  
| 文字列 | 文字列です。クォートはあってもなくてもよいです。  | `foo`, `"bar"`  |  
| 色  | RGB値や色の名前です。  | `#4ff`, `blue`  |  
| 真偽値 | trueまたはfalseです。 | `true` |  
| null | nullです。 | `null` |  
| リスト | スペースまたはカンマ区切りの値です。 | `1 1 auto`, `Helvetica,Sans` |  
| マップ | キーと値の組です。言語によっては、連想配列やハッシュともいいます。 | `(key1:value1, key2:value2)` |  
| 関数への参照 | 関数への参照です。`call`で呼び出せます。 | `$ref = get-function("関数名")` |  
  
# `@`で始まるディレクティブ達  
  
## `@import`  
  
外部ファイルを読み込んで、全体をひとつのcssファイルにします。  
  
## `@mixin`と`@include`  
  
変数は値を使いまわすための仕組みでした。  
mixinはスタイル全体を使いまわすための仕組みです。  
  
**mixinの宣言**  
```scss:mixinの宣言
@mixin 強調 {
  font-size: 1.4em;
  font-weight: bold;
}
```  
  
mixinで定義したスタイルは`@include`で呼び出せます。  
  
**mixinを使う**  
```scss:mixinを使う
.my-em {
  @include 強調;
}
```  
  
mixinは引数付きで宣言することもできます。  
  
**引数付きのmixin**  
```scss:引数付きのmixin
@mixin 色付き枠($color) {
  border-style: solid;
  border-width: 1px;
  border-color: $color;
}
```  
  
使うときは引数を渡します。  
  
**引数付きのmixinを使う**  
```scss:引数付きのmixinを使う
@include 色付き枠(green);
```  
  
# `@function`   
  
関数を宣言することができます。  
  
# 関数  
  
様々な便利関数が定義されています。  
  
* `rgb($red, $green, $blue)` RGB値を色にします。  
* `map_get($map, $key)` マップから指定されたキーに対応する値を返します。  
  
  
その他はこちら [関数の一覧](https://sass-lang.com/documentation/Sass/Script/Functions.html)  
  
  
# ちょっと  
  
わかりました :smile:   
