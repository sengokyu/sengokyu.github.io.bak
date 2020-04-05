---
date: '2017-02-20T14:22:36+09:00'
layout: post
published: true
qiita_article_id: 48f58f778a05f13f922e
tags:
- C#
- .NET
- テスト
- NSubstitute
title: '[C#][.NET] モックライブラリNSubstitute使い方メモ'
updated: '2018-02-09T20:58:08+09:00'

---
.Net向けのモックライブラリは数あります。そのうちNSubstituteは、staticクラスや拡張メソッドをモック化できて便利なのです。  
  
# インストールの仕方  
  
Visual Studioであれば、プロジェクトを右クリック > Manage NuGet Packages... メニューを選択、あとは「NSubstitute」を検索してインストールするだけです。  
  
# モックの生成  
  
まずはモックの生成から。  
  
****  
```csharp:
var mock = NSubstitute
    .Substitute
    .For<モック対象クラス>();
```  
  
# メソッド戻り値の定義  
  
## 引数がない場合  
  
****  
```csharp:
mock.SomeMethod()
    .Returns(戻り値);
```  
  
## 引数を指定する場合  
  
****  
```csharp:
mock.SomeMethod(NSubstitute.Arg.Is(引数))
    .Returns(戻り値);
```  
  
## 引数はなんでもいい場合  
  
****  
```csharp:
mock.SomeMethod(NSubstitute.Arg.Any<型>())
    .Returns(戻り値);
```  
  
# プロパティ戻り値の定義  
  
****  
```csharp:
mock.SomeProperty
    .Returns(戻り値);
```  
  
# メソッド呼び出しのベリファイ  
  
メソッドが呼び出されたかどうかテストします。  
  
****  
```csharp:
mock.Received()
    .SomeMethod();
```  
  
## 呼び出し回数もベリファイ  
  
3回呼び出されたかどうかテストします。  
  
****  
```csharp:
mock.received(3)
    .SomeMethod();
```  
  
## 余計な呼び出しが行われていないか  
  
****  
```csharp:
mock.DidNotReceived()
    .SomeMethod();
```  
  
# メソッド呼び出し時の引数を捕まえる (capture arguments)  
  
このようなテスト対象のクラスがあったとしまして、  
  
****  
```csharp:
class Foo
{
    Result Bar(Baz baz)
    {
        // some stuff
    }
}
```  
  
こうします。  
  
****  
```csharp:
var capturedArgs = new List<Baz>();

mock.Bar(Arg.Do<Baz>(x => capturedArgs.Add(x)));
```  
  
`capturedArgs`に引数がたまります。  
  
:warning: 引数のインスタンスが変化するようなときは、クローンを作らないとテストが期待通りにならないかもしれません。  
