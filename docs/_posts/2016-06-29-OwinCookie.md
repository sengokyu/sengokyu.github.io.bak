---
date: '2016-06-29T17:50:12+09:00'
layout: post
published: true
qiita_article_id: ee4621eaa6ffe105696f
tags:
- ASP.NET
- OWIN
title: OwinにおいてCookieを使った認証処理で使われているソースへのリンク集
updated: '2016-06-29T17:50:12+09:00'

---
# microsoft.owin.security  
  
- AuthenticationManager<br>  
https://katanaproject.codeplex.com/SourceControl/latest#src/Microsoft.Owin/Security/AuthenticationManager.cs  
  
  
# micorsoft.owin.security.cookie  
  
- IAppBuilderへの拡張メソッド UseCookieAuthentication<br>  
https://katanaproject.codeplex.com/SourceControl/latest#src/Microsoft.Owin.Security.Cookies/CookieAuthenticationExtensions.cs  
- CookieAuthenticationOptions.CookieManagerのデフォルト値<br>  
https://katanaproject.codeplex.com/SourceControl/latest#src/Microsoft.Owin/Infrastructure/ChunkingCookieManager.cs  
- UseCookieAuthenticationで設定されるミドルウェア<br>  
https://katanaproject.codeplex.com/SourceControl/latest#src/Microsoft.Owin.Security.Cookies/CookieAuthenticationMiddleware.cs  
- リクエストとレスポンスのハンドラ<br>  
https://katanaproject.codeplex.com/SourceControl/latest#src/Microsoft.Owin.Security.Cookies/CookieAuthenticationHandler.cs  
  
  
