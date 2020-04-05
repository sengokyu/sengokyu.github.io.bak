---
date: '2016-10-25T19:21:18+09:00'
layout: post
published: true
qiita_article_id: 7f9f0f0904a3df9b1a3c
tags:
- C#
- VisualStudio
- InternetExplorer
- BrowserHelperObject
- BHO
title: '[IE][BHO] Internet Explorer Add-on (Browser Helper Object - BHO)をVisual Studio
  2013でデバッグする方法'
updated: '2016-10-25T19:22:44+09:00'

---
古のテクノロジーを再び目覚めさせなければならなくなった未来人へ……。  
  
# 環境  
  
- Windows 10 Professional (x64)  
- Visual Studio 2013 Professional Update 5  
- Internet Explorer 11 (x86)  
  
# やるべきこと  
  
やるべきことを列挙します。  
  
- Visual StudioをAdministrator権限で起動する（次でCOMを登録するため）  
- プロジェクトのプロパティ＞ビルド＞Register for COM interopをチェックする  
- IObjectWithSiteのSetSite内で、Debugger.Launch()する  
  
  
以下詳細です。  
  
## Visual StudioをAdministrator権限で起動する  
  
ビルドと同時にBHO COMを登録するために、Visual StudioをAdministrator権限で起動します。  
  
手動で登録する場合は、一般ユーザ権限で問題ありません。  
  
手動での登録にはregasmコマンドを使います。  
  
```
regasm DLLファイル名 /codebase
```  
  
## プロジェクトのプロパティ＞ビルド＞Register for COM interopをチェックする  
  
[PROJECT]-[プロジェクトのプロパティ]メニューを開きます。  
  
[ビルド]タブを選択し、[Register for COM interop]をチェックします。  
  
![debugbho1.PNG](/assets/images/48e756bf-2c27-bf5d-0896-d37e659ba0d6.png)  
  
## IObjectWithSiteのSetSite内で、Debugger.Launch()する  
  
IEを起動したとき、Visual Studioのデバッガへ自動接続するようにします。  
  
IObjectWithSiteインターフェースを実装するクラスの中で、以下のようにします。  
  
****  
```csharp:
public void SetSite(Object site)
{
# if DEBUG
    System.Diagnostics.Debugger.Launch();
# endif
    // some code
}
```  
  
手動でやるときは、Visual Studioから[DEBUG]-[Attach to Process...]メニューを開いて、IEを選択します。  
  
  
# デバッグ時の手順  
  
1. IEが起動していたら終了する  
2. Visual Studioでビルドする  
3. IEを起動する  
4. デバッガの選択画面が表示されるので、使用しているVisual Studioを選ぶ  
  
# 落穂拾い  
  
登録してしまったBHO COMは、以下のようにして登録解除できます。  
  
```
regasm DLLファイル名 /unregister
```  
