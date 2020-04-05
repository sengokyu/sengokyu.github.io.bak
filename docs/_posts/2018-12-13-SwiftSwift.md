---
date: '2018-12-13T12:50:14+09:00'
layout: post
published: true
qiita_article_id: 194cc72f1e974da00332
tags:
- Swift
title: '[Swift] 他の言語からやってきたひと（自分）のための、拙速Swiftコードリーディング'
updated: '2018-12-13T12:50:14+09:00'

---
初めてSwiftのコードを眺めたとき、なんなく想像ができるものがあれば、全くわからないものもあります。  
よくわからなかったものをあげていきます。  
  
  
# `let` `var`  
  
`let`は定数です。  
`var`は変数です。  
  
JavaScriptと同時に書いていると混乱 :sweat:   
なぜconstでないのか。。。  
  
  
# `_`  
  
Swiftは関数呼び出し時に引数名を付ける必要があります。関数宣言時に`_`を付けておくと、引数名を省略できます。  
  
****  
```swift:
// _ がないとき
func myFunc(arg: String) {}
myFunc(arg: "arg") // 名前付きで呼び出す

// _ があるとき
func myFunc(_ arg: String) {}
myFunc("arg") // 名前を省略できる
```  
  
また、関数からの戻り値を捨てるときに使います。  
  
****  
```swift:
_ = MyFunc()
```  
  
  
# `?` と `!`  
  
使われている場所によって意味が違います。  
  
## 変数宣言  
  
オプショナル型(Null許容型)の変数を宣言します。  
  
```swift
let hoge: String?
let hoge: String!
```  
  
`!` のほうは、使うときに自動的にオプショナルが外れます。  
  
## 変数の参照  
  
変数がnullのときはプロパティやメソッドを呼び出しません。  
  
****  
```swift:
let length = hoge?.length
let upper = hoge?.toUpper()
```  
  
## 普通の型にする  
  
`!` を付けるとオプショナル型ではなくなります。  
  
****  
```swift:
let hoge: String?
let fuga: String = hoge!
```  
  
# `$0`  
  
省略された第1引数です。  
  
****  
```swift:
var doubled = myArray.map { $0 * 2 }
```  
  
# `...` `..<`  
  
****  
```swift:
0...10 // 0以上10以下
0..<10 // 0以上10未満
```  
  
  
# 他にもわからないものいろいろ  
  
また調べて書こうと思います。  
  
`internal`   
`private`  
`fileprivate`  
`public`  
`open`  
`convenience`  
`weak`  
`extension`  
  
  
  
  
  
