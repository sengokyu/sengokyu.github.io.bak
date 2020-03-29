---
'created_at: ': '2019-04-20T23:02:54+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: ASP.NET_MVC
      versions: []
    - name: .NETCore
      versions: []
    - name: ASP.NET_Core
      versions: []
  state: 0
title: "[ASP.NET Core MVC] \u30B5\u30FC\u30D3\u30B9\u30B3\u30EC\u30AF\u30B7\u30E7\u30F3\
  \uFF08DI\u30B3\u30F3\u30C6\u30CA\uFF09\u306E\u69CB\u6210\u65B9\u6CD5 AddMvc/AddMvcCore/AddController/AddControllersWithView\
  \ \u306E\u9055\u3044"
'updated_at: ': '2019-04-20T23:02:54+09:00'

---
# これはなに？  
  
ASP.Net Coreのサービスコレクション（DIコンテナ）にMVC機能を載せる拡張メソッドはいくつかあります。  
  
* `AddMvcCore`  
* `AddControllers`  
* `AddControllersWithView`  
* `AddMvc`  
  
どう違うのかドキュメントがないので、ソースコードから探ってみます。  
  
__2019年4月__時点の情報です。  
  
  
# TL;DR  
  
| 拡張メソッド | 解説 |  
|:--|:--|  
| `AddMvcCore`  | 必要最小限のものを追加  |  
| `AddControllers`  | `AddMvcCore` + いろいろ（後述）  |  
| `AddControllersWithView`  | `AddControllers` + `AddViews` + `AddRazorViewEngine`  |  
| `AddMvc`  | 全部入り `AddControllersWithView` + `AddRazorPages`  |  
  
  
  
# ソースコードのありか  
  
ソースコード自体はこちらにあります。  
  
* [AddMvc拡張メソッド](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc/src/MvcServiceCollectionExtensions.cs)  
* [AddMvcCore拡張メソッド](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/DependencyInjection/MvcCoreServiceCollectionExtensions.cs)  
  
# `AddControllers`がやっていること  
  
`AddMvcCore`に加えて、以下のことをやっています。  
  
```c#
AddApiExplorer();
AddAuthorization();
AddCors();
AddDataAnnotations();
AddFormatterMappings();
```  
  
なんとなく文字面から想像できるでしょうか。  
  
`AddAuthorization` 認証/認可周りの処理をやってくれそうです。  
  
`AddCors` クロスオリジンな処理をやってくれそうです。  
  
`AddDataAnnotations` モデルの検証（バリデーション）周りをやってくれそうです。  
  
`AddFormatterMapping` URL文字列と、メディアタイプの関連付けをやってくれるようです。  
  
`AddApiExplorer`、、は謎ですね。  
  
  
# まとめ  
  
なんとなく雑にわかったのでまとめてみます。  
  
* Razorビューを使うひと → 全部入り`AddMvc`を使う  
* ビューが不要なひと → `AddControllers`か`AddMvcCore`を使う  
* WebAPIのみなひと → `AddControllers`か`AddMvcCore`を使う  
