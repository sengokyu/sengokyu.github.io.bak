---
date: '2020-01-14T10:34:18+09:00'
layout: post
published: true
qiita_article_id: 6b54ad52e86de1d0e7b6
tags:
- C#
- JWT
- ASP.NET_Core
title: '[Microsoft] ASP.NET CoreでJWT認証するそこそこシンプルなサンプル（統合テスト付き）'
updated: '2020-01-15T15:21:49+09:00'

---
# これはなに？  
  
ASP.NET Coreにおいて、JWTトークンを発行してもらうコードと、JWTトークンを検証するコードのサンプルです。  
  
dotnetのバージョンは、すでにサポート切れの2.2です。  
たぶん3.1でもそんなに変わらないはずです。  
  
統合テストも作っています。  
  
# サンプルプログラム  
  
プログラム全体はこちらに置いています。  
https://github.com/sengokyu/Ex.JwtAuth  
  
`git clone`して`dotnet test ExJwtAuth.Tests`としてもらえれば、テストが動きます。  
  
## JWTトークンを発行してもらうコード  
  
ログインIDとパスワードを受け付けて、JWTトークンをJSONとして返しています。  
ログインIDとパスワードを受け付けていますが、何もしておらず素通しです。  
  
JWTトークンは、`System.IdentityModel.Tokens.Jwt.JwtSecurityTokenHandler`クラスを使用すると生成できます。  
  
（サンプルは、すべてをASP.NET MVCのコントローラの中でやっています。いわゆるファットコントローラというやつです。よいこは真似してはダメです）。  
  
  
**ExJwtAuth/Controllers/AuthenticationController.cs**  
```c#:ExJwtAuth/Controllers/AuthenticationController.cs
        [AllowAnonymous]
        [HttpPost]
        public IActionResult Login(
            [Required][FromBody]LoginParam loginParam
        )
        {
            var handler = new JwtSecurityTokenHandler();
            // JWT内に入れるクレームです。
            var claims = new[] {
                new Claim(ClaimTypes.Name, loginParam.Username)
            };
            var subject = new ClaimsIdentity(claims);
            var credentials = new SigningCredentials(
                JwtSecurityConfiguration.SecurityKey,
                SecurityAlgorithms.HmacSha256);
            // ここでトークンを生成しています。
            var token = handler.CreateJwtSecurityToken(
                audience: JwtSecurityConfiguration.Audience,
                issuer: JwtSecurityConfiguration.Issuer,
                subject: subject,
                signingCredentials: credentials);
            var tokenText = handler.WriteToken(token);
            var result = new
            {
                token = tokenText
            };

            return Ok(result);
        }
```  
  
## JWTトークンを検証するコード  
  
JWTトークンの検証は、あらかじめ用意されている機能を追加するだけです。  
`Startup`クラスの中で有効化します。  
  
`AddJwtBearer`メソッドに渡すオプションは、また別途DIするようにしています。  
  
**ExJwtAuth/Startup.cs**  
```c#:ExJwtAuth/Startup.cs
        public void ConfigureServices(IServiceCollection services)
        {
            services
                .AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, configureOptions: null);
// 省略
            // JwtBearerOptionsの設定は別クラスでやる
            services
                .AddSingleton<IConfigureOptions<JwtBearerOptions>, JwtBearerConfigureOptions>();

        }
```  
  
追加した機能を有効にします。  
Useする順番大事です。  
  
**ExJwtAuth/Startup.cs**  
```c#:ExJwtAuth/Startup.cs
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            app
                .UseAuthentication()
                .UseMvc();
        }
```  
  
## JWTを検証する設定  
  
Webサーバに渡されたJWTを検証する設定は別クラスにしました。  
AudienceもIssurerも検証するようにしています。  
  
Audienceの検証は、手抜きして何が来てもOKなようにしています。  
実際はデータベースを検索したりします。  
  
**Configurations/JwtBearerConfigureOptions.cs**  
```c#:Configurations/JwtBearerConfigureOptions.cs
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Extensions.Options;
using Microsoft.IdentityModel.Tokens;

namespace ExJwtAuth.Configuratins
{
    public class JwtBearerConfigureOptions : IConfigureNamedOptions<JwtBearerOptions>
    {
        public void Configure(string name, JwtBearerOptions options)
        {
            if (name != JwtBearerDefaults.AuthenticationScheme)
            {
                return;
            }

            options.TokenValidationParameters = new TokenValidationParameters
            {
                AudienceValidator = this.AudienceValidatorDelegate,
                ValidIssuer = JwtSecurityConfiguration.Issuer,
                IssuerSigningKey = JwtSecurityConfiguration.SecurityKey,
                ValidateAudience = true,
                ValidateIssuer = true,
                ValidateIssuerSigningKey = true,
            };
        }

        public void Configure(JwtBearerOptions options)
        {
            Configure(JwtBearerDefaults.AuthenticationScheme, options);
        }

        public bool AudienceValidatorDelegate(IEnumerable<string> audiences, SecurityToken securityToken, TokenValidationParameters validationParameters)
        {
            // 実際は、データベースを見たりする。
            return true;
        }
    }
}
```  
  
  
## ログイン状態を検証するコード  
  
ログイン状態を検証するために、ログインしているユーザ名をJSONで返すコードを追加しました。  
  
**ExJwtAuth/Controllers/AuthenticatinController.cs**  
```c#:ExJwtAuth/Controllers/AuthenticatinController.cs
        [Authorize()]
        [HttpGet]
        public IActionResult GetAction()
        {
            var username = HttpContext.User.Identity.Name;
            var result = new
            {
                username = username
            };

            return Ok(result);
        }
```  
  
# 統合テスト  
  
統合テストでは、以下の内容をテストしています。  
  
* JWTトークン無しでアクセスして HTTP 401 が返ること  
* ログインID/パスワードをPOSTしてJWTトークンが返ること  
* JWTトークン付きでアクセスしてログインIDが返ること  
  
テストのアサーションには、FluentAssertionsを使用しています。`Assert.Equal(期待値, 実際の値)`みたいに書くよりは、書きやすく読みやすいのではないでしょうか。  
  
## テストクラスの雛形  
  
テストクラスは`IClassFixture`を実装します。xUnitテストランナーに対して、テスト間で情報を持ちまわるようにすることを指示しています。  
  
コンストラクタで`WebApplicationFactory`クラスを受け取ります。このクラスを使ってHTTPクライアントを生成します。  
  
```c#
    public class AuthenticationApiTests
        : IClassFixture<WebApplicationFactory<Startup>>
    {
        private readonly WebApplicationFactory<Startup> factory;

        public AuthenticationApiTests(WebApplicationFactory<Startup> factory)
        {
            this.factory = factory;
        }
    }
```  
  
  
## JWTトークン無しでアクセスして HTTP 401 が返ることを確認するテスト  
  
`[Fact]`をつけたものが、xUnitに認識されて実行されます。  
  
HTTP Clientを作って、トップページへアクセスし、HTTPステータスコードを確認しています。  
  
```c#
        [Fact]
        public async Task Test未認証だと401()
        {
            // Given
            var uri = "/";

            using (var client = factory.CreateClient())
            {
                // When
                var response = await client.GetAsync(uri);

                // Then
                response.StatusCode.Should().Be(HttpStatusCode.Unauthorized);
            }
        }
```  
  
## その他のテスト  
  
長くなるので省略します。githubを見てください。  
https://github.com/sengokyu/Ex.JwtAuth/blob/master/ExJwtAuth.Tests/AuthenticationApiTests.cs  
  
  
# JWT認証に必要なパッケージ  
  
以下のパッケージが追加で必要でした。  
  
* [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/)  
* [Microsoft.AspNetCore.Authentication.JwtBearer](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.JwtBearer/)  
  
# リンク  
  
* [JSON Web Token（JWT）のClaimについて](https://kamichidu.github.io/post/2017/01/24-about-json-web-token/) JWTに入れるクレームの解説があります。   
  
  
# おまけ  
  
テストクラスのテストメソッド名を日本語にするのは、結構アリだと思うんですけどどうでしょう？  
  
  
