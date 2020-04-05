---
date: '2019-04-04T20:56:11+09:00'
layout: post
published: true
qiita_article_id: a9bb2e3e235872a575cf
tags:
- Windows
- Linux
- macos
- .NETCore
title: '[.Net Core] クロスプラットホーム開発の注意点 - TimeZoneInfo'
updated: '2019-04-04T20:56:11+09:00'

---
# TO;DR  
  
* TimeZone IDはプラットホームにより異なります。  
* `TimeZoneConverter`パッケージを使いましょう。  
  
# 問題  
  
`TimeZoneInfo.FindSystemTimeZoneById`(https://docs.microsoft.com/ja-jp/dotnet/api/system.timezoneinfo.findsystemtimezonebyid?view=netcore-2.2) に渡す引数は、プラットホームにより異なります。  
  
* macOSやLinux → IANAで決められた文字列  
* Windows → 独自(?)文字列  
  
# 回避策  
  
`TimeZoneConverter` https://www.nuget.org/packages/TimeZoneConverter/ パッケージを使います。  
  
```c#
var tz = TZConvert.GetTimeZoneInfo("Tokyo Standard Time");
```  
  
# 詳細  
  
* [Cross-platform Time Zones with .NET Core](https://devblogs.microsoft.com/dotnet/cross-platform-time-zones-with-net-core/)  
