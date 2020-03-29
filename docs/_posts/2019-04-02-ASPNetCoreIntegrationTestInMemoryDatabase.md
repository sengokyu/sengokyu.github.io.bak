---
'created_at: ': '2019-04-02T22:10:41+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: EntityFramework
      versions: []
    - name: ASP.NET_Core
      versions: []
  state: 0
title: "[ASP.Net Core] \u7D71\u5408\u30C6\u30B9\u30C8(Integration Test)\u3067\u30E1\
  \u30E2\u30EA\u5185\u30C7\u30FC\u30BF\u30D9\u30FC\u30B9(InMemoryDatabase)\u3092\u4F7F\
  \u3046\u3068\u304D\u306E\u6CE8\u610F\u70B9"
'updated_at: ': '2019-04-02T22:10:41+09:00'

---
# TL;DR  
  
* InMemoryDatabaseはサービスプロバイダを共有しない  
* したがってテスト間でデータが共有されない  
* `InMemoryDatabaseRoot`を使って解決する  
  
  
# 前提  
  
統合テスト起動時、データベースへ初期データを登録しています。  
  
**CustomWebApplicationFactory.cs**  
```c#:CustomWebApplicationFactory.cs
public class CustomWebApplicationFactory : WebApplicationFactory<Startup>
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.ConfigureServices(services =>
        {
            services.AddDbContext<MyDBContext>(options =>
            {
                // InMemoryDatabaseを使う
                options.UseInMemoryDatabase("MyTest");
            });
        });

        builder.Configure(app =>
        {
            // DBContextはrootにはなく別スコープにあるので、
            // スコープを作らないと取得できない。
            using (var sp = app.ApplicationServices.CreateScope())
            {
                var context = sp.ServiceProvider.GetRequiredService<MyDBContext>();

                // ここでデータベースへ初期データを登録

                context.SaveChanges();
            }
        });
     }
}
```  
  
`WebApplicationFactory`は、`Microsoft.AspNetCore.Mvc.Testing`パッケージに用意されているクラスです。`TestServer`の作成等をやってくれます。  
  
  
# 問題  
  
コントローラにインジェクションされるDBコンテキストを見ても、登録したデータが入っていません。  
  
# 解決策  
  
Issueが上がっていました。  
https://github.com/aspnet/EntityFrameworkCore/issues/9613#issuecomment-430722420  
  
先ほどの`CustomWebApplicationFactory`クラスを変更します。  
  
**CustomWebApplicationFactory.cs**  
```c#:CustomWebApplicationFactory.cs
public class CustomWebApplicationFactory : WebApplicationFactory<Startup>
{
    public static readonly InMemoryDatabaseRoot InMemoryDatabaseRoot
    = new InMemoryDatabaseRoot();

    // （省略）
            services.AddDbContext<MyDBContext>(options =>
            {
                options.UseInMemoryDatabase("MyTest", InMemoryDatabaseRoot);
            });
    // （省略）

}
```  
  
`UserInMemoryDatabase`メソッドに、`InMemoryDatabaseRoot`オブジェクトを渡してあげればよいそうです。  
