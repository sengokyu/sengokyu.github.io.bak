---
'created_at: ': '2019-04-04T20:56:11+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Windows
      versions: []
    - name: Linux
      versions: []
    - name: macos
      versions: []
    - name: .NETCore
      versions: []
  state: 0
title: "[.Net Core] \u30AF\u30ED\u30B9\u30D7\u30E9\u30C3\u30C8\u30DB\u30FC\u30E0\u958B\
  \u767A\u306E\u6CE8\u610F\u70B9 - TimeZoneInfo"
'updated_at: ': '2019-04-04T20:56:11+09:00'

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
