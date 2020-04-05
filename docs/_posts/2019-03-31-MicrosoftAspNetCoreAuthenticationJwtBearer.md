---
date: '2019-03-31T14:50:26+09:00'
layout: post
published: false
qiita_article_id: 586973f83b1ef30b231d
tags:
- C#
- JWT
- ASP.NET_Core
title: '[メモ] Microsoft.AspNetCore.Authentication.JwtBearer'
updated: '2019-03-31T14:51:51+09:00'

---
__:warning:この文書は書きかけです:warning:__  
  
認証が必要なWeb APIを作ります。  
認証情報は、JWT(Json Web Token)を使って渡してもらうようにします。  
  
# 環境  
  
* .NET Core SDK  
    *  2.2.105  
* .NET Core runtime  
    *  Microsoft.AspNetCore.All 2.2.3  
    *  Microsoft.AspNetCore.App 2.2.3  
    *  Microsoft.NETCore.App 2.2.3  
  
  
  
# 準備  
  
ソリューションとプロジェクトを生成します。  
プロジェクトにパッケージを追加します。  
  
```console
dotnet new sln --name Ex.Auth.JwtBearer --output Ex.Auth.JwtBearer
cd Ex.Auth.JwtBearer
dotnet new webapi --name Ex.Auth.JwtBearer
dotnet add Ex.Auth.JwtBearer package Microsoft.AspNetCore.Authentication.JwtBearer
dotnet sln add Ex.Auth.JwtBearer
```  
  
# JwtBearerを使う  
  
## サービスに追加  
  
Startupクラスを開き、ConfigureServicesメソッドに追記します。  
  
**Starup.cs**  
```c#:Starup.cs
services.AddAuthentication(options =>
{
    options.DefaultScheme = JwtBearerDefaults.AuthenticationScheme;
})
.AddJwtBearer();
```  
  
## APIを認証必須にする  
  
コントローラクラスのメソッドに`[Authorize]`属性をつければよいです。  
  
  
# 仕組み  
  
## JWTの読み込み  
  
Authorization HTTPヘッダに格納されててやってきたJWTは、`JwtBearerHandler`[(ソース)](https://github.com/aspnet/AspNetCore/blob/v2.2.3/src/Security/Authentication/JwtBearer/src/JwtBearerHandler.cs)クラスの`HandlerAuthenticateAsync`メソッドの中で読み込んでいます。  
  
JWTのデコードは、[System.IdentityModel.Tokens.Jwt](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/)パッケージの`JwtSecurityTokenHandler`[(ソース)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/5.4.0/src/System.IdentityModel.Tokens.Jwt/JwtSecurityTokenHandler.cs)クラスから呼び出される`JwtSecurityToken`[（ソース）](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/5.4.0/src/System.IdentityModel.Tokens.Jwt/JwtSecurityToken.cs)クラスの`Decode`メソッドで行なっています。  
  
  
  
