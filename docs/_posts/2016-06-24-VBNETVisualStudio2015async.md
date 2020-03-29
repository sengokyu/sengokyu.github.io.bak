---
'created_at: ': '2016-06-24T15:01:18+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: VB.Net
      versions: []
    - name: VisualStudio
      versions: []
  state: 0
title: "[VB.NET][Visual Studio 2015]async\u304C\u4ED8\u3044\u3066\u3044\u308B\u975E\
  \u540C\u671F\u30E1\u30BD\u30C3\u30C9\u3092\u30C6\u30B9\u30C8\u3059\u308B"
'updated_at: ': '2016-06-24T15:01:18+09:00'

---
`async`が付いている非同期メソッドをテストする話です。  
  
# 環境  
  
- Visual Studio 2015 Community Update 2  
- VB.NET  
  
# 前提  
  
例えば、こんな感じに定義された非同期メソッドがあったとします。  
  
**Foo.vb**  
```vbnet:Foo.vb
Public Class Foo
    Public Async Shared Function BarAsync() As Task
      ' ...
    End Function
End Class
```  
  
# 動かないテスト  
  
そこでこのようなテストを書きました。  
  
**FooTest.vb**  
```vbnet:FooTest.vb
<TestClass()>
Public Class FooTest
    <TestMethod()>
    Public Sub TestBarAsync()
        Foo.BarAsync()
    End Sub
End Class
```  
  
が、何が起こってもテストは成功したと報告してきます。  
asyncのせいです。  
  
# 実行されないテスト  
  
そこで非同期メソッド呼び出し部分にAwaitを付けると、テストが実行されません。  
  
**FooTest.vb**  
```vbnet:FooTest.vb
<TestClass()>
Public Class FooTest
    <TestMethod()>
    Public Async Sub TestBarAsync()
        Await Foo.BarAsync()
    End Sub
End Class
```  
  
よく見ると、こんなメッセージが出ています。  
  
  
> Method TestBarAsync defined in class FooTest does not have correct signature. Test method marked with the [TestMethod] attribute must be non-static, public, return-type as void  and should not take any parameter. Example: public void Test.Class1.Test(). __Additionally, return-type must be Task if you are running async unit tests. Example: public async Task Test.Class1.Test2()__  
  
テストメソッドをasyncにしたときは、Taskを返さないといけないです。  
  
# Taskを返す  
  
テストメソッドをSubからFunctionにして、戻り値をTaskにしたところ動きました。  
  
**FooTest.vb**  
```vbnet:FooTest.vb
<TestClass()>
Public Class FooTest
    <TestMethod()>
    Public Async Function TestBarAsync() As Task
        Await Foo.BarAsync()
    End Sub
End Class
```  
  
# 参考リンク  
  
- 非同期プログラミング 非同期コードの単体テスト   
https://msdn.microsoft.com/ja-jp/magazine/dn818493.aspx  
- async awaitを使ったテストの単体テスト  
http://blog.okazuki.jp/entry/20120910/AsyncAwait_UnitTest  
