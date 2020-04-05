---
date: '2016-06-22T15:00:34+09:00'
layout: post
published: true
qiita_article_id: 3b0cfaa226aeb0dcc319
tags:
- VB.Net
- ASP.NET
- ASP.NET_WebAPI
title: ASP.NET Web API事始め[VB.Net]
updated: '2016-06-22T17:59:45+09:00'

---
Visual Studio 2013でASP.Net Web APIを始めました。  
  
が、すでに終わることが決まっているようです。  
  
開発言語は都合によりVB.Netです。よいこはC#を使いましょう。  
  
# 名前（用語）は大事だよー  
  
Microsoftは同じものに違う名前を付けたり、違うものに同じ名前を付けたり、そんなことがしょっちゅうなので、まずは整理したいと思います。  
  
## ASP.NET  
  
.Net Frameworkを使用してASPアプリを作るためのフレームワーク。  
2016年時点の最新バージョンは4.6。  
  
## ASP.NET Core  
  
.Net Frameworkからプラットホーム依存部分を取り除いた.Net Coreで動くASP.NET。  
.Net Framework 4.6でも動く。  
  
## ASP.NET 5  
  
ASP.NET Core 1.0のこと。  
  
## ASP.NET vNext  
  
ASP.NET 5のこと。  
  
## ASP.NET Web API  
  
Restfulなアプリケーションを作るためのフレームワーク。  
ASP.NET MVC 4と共に登場した。  
2016年時点の最新バージョンは2.2。  
ASP.NET MVC 6に統合されディスコン予定。  
  
## ASP.NET MVC  
  
MVCなアプリケーションを作るためのフレームワーク。  
2016年時点の最新バージョンは5.2。  
  
## OWIN  
  
Open Web Interface for .NET  
IIS非依存を目指して作られた規格。  
ASP.NET 5で捨てられた。  
  
## Katana  
  
OWINの実装。  
ディスコン。  
  
  
# リンク集  
  
## ASP.NET Web APIのHTTPメッセージライフサイクル  
  
http://www.asp.net/media/4071077/aspnet-web-api-poster.pdf  
  
HTTPメッセージの入力がどこを通ってどのように出ていくかの表です。  
最初にこれを見るとよいかも。  
  
## ASP.NETのソースコードリポジトリ  
  
何かうまくいかないことがあるとき、ドキュメントを探すのが大変なとき、ソースを見るのが手っ取り早いです。  
  
- ASP.NET MVC / ASP.NET Web API https://aspnetwebstack.codeplex.com/  
- ASP.NET Core https://github.com/aspnet/home  
- ASP.NET Identity V3 https://github.com/aspnet/Identity/  
- ASP.NET Identity V2 https://aspnetidentity.codeplex.com/  
  
## Introduction to ASP.NET Identity  
  
認証フレームワークであるところのASP.NET Identityの説明です。  
  
http://www.asp.net/identity/overview/getting-started/introduction-to-aspnet-identity  
  
## Dependency Injection関係  
  
### Unity DI 3 で追加された ASP.NET MVC と Web API 向けライブラリ  
  
ASP.NET MVCとASP.NET Web APIにUnityを絡めるライブラリの説明です。  
  
http://blog.shibayan.jp/entry/20130428/1367120130  
  
### ASP.NET MVC 5 で DI する - その3「VBで利用する」 #aspnetjp   
  
上記ライブラリはC#向けなので、VBで使う方法です。  
  
http://katsuyuzu.hatenablog.jp/entry/2014/11/21/001129  
  
### Unity bootstrap ASP.NET Web API  
  
上記リンク先で使用しているライブラリです。  
  
http://unity.codeplex.com/  
  
# こんな環境で始めました  
  
- Visual Studio ~~2013~~ 2015  
- ASP.Net Web API 2.2 (インストール時の指定バージョンは5.2.3)  
  
