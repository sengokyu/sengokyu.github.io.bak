---
date: '2019-04-20T23:02:54+09:00'
layout: post
published: true
qiita_article_id: 6c30d1de09a62e28348f
tags:
- ASP.NET_MVC
- .NETCore
- ASP.NET_Core
title: '[ASP.NET Core MVC] サービスコレクション（DIコンテナ）の構成方法 AddMvc/AddMvcCore/AddController/AddControllersWithView
  の違い'
updated: '2019-04-20T23:02:54+09:00'

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
