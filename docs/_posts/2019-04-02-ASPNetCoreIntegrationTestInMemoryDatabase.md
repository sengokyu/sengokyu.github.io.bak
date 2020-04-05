---
date: '2019-04-02T22:10:41+09:00'
layout: post
published: true
qiita_article_id: 4fd495afcc138d7afcfb
tags:
- EntityFramework
- ASP.NET_Core
title: '[ASP.Net Core] 統合テスト(Integration Test)でメモリ内データベース(InMemoryDatabase)を使うときの注意点'
updated: '2019-04-02T22:10:41+09:00'

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
