---
date: '2019-01-23T16:28:37+09:00'
layout: post
published: true
qiita_article_id: 9ae4bfd43f6660abb935
tags:
- C#
- ASP.NET
- ASP.NET_Core
title: '[Microsoft] ASP.NET Core サーバが起動するまでを追いかけてみる'
updated: '2019-05-17T13:15:26+09:00'

---
ASP.NET Coreを理解するために、プログラムが起動するまでを追ってみます。  
  
テンプレートから生成されたファイルを題材にします。  
  
  
# プログラムエントリ  
  
## `Program#Main`  
  
`Program`クラスの`Main`メソッドの中は、以下のようになっています。  
  
これを1行ずつ追ってみます。  
  
**Program.cs**  
```c#:Program.cs
WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .Build()
    .Run();
```  
  
# 1行目: `.CreateDefaultBuilder(args)`  
  
## `WebHostBuilder`インスタンスの作成  
  
`WebHostBuilder`インスタンスを作成します。  
  
ソースファイル: https://github.com/aspnet/AspNetCore/blob/master/src/DefaultBuilder/src/WebHost.cs  
  
```c#
var builder = new WebHostBuilder();
```  
  
## 環境変数を読み込むようにする  
  
`WebHostBuilder`コンストラクタの主要部分です。  
  
`ConfigurationBuilder`の拡張メソッドを呼んで、プレフィックス **ASPNETCORE_** がついた環境変数を読み込むようにします。  
  
ソースファイル: https://github.com/aspnet/AspNetCore/blob/master/src/Hosting/Hosting/src/WebHostBuilder.cs  
  
```c#
_hostingEnvironment = new HostingEnvironment();

_config = new ConfigurationBuilder()
    .AddEnvironmentVariables(prefix: "ASPNETCORE_")
    .Build();

_context = new WebHostBuilderContext
{
    Configuration = _config
};
```  
  
### `ConfigurationBuilder#AddEnvironmentVariables`  
  
指定されたプレフィックスがついた環境変数を読み込むようにします。  
  
ソースファイル: https://github.com/aspnet/Extensions/blob/master/src/Configuration/Config.EnvironmentVariables/src/EnvironmentVariablesExtensions.cs  
  
```c#
public static IConfigurationBuilder AddEnvironmentVariables(
    this IConfigurationBuilder configurationBuilder,
    string prefix)
{
    configurationBuilder.Add(new EnvironmentVariablesConfigurationSource { Prefix = prefix });
    return configurationBuilder;
}
```  
  
  
## ドキュメントルートをカレントディレクトリにする  
  
```c#
if (string.IsNullOrEmpty(builder.GetSetting(WebHostDefaults.ContentRootKey)))
{
    builder.UseContentRoot(Directory.GetCurrentDirectory());
}
```  
  
## コマンドライン引数を処理  
  
`dotnet run`コマンドでは何も渡ってこないので、実質何もしてないです。  
  
**src/DefaultBuilder/src/WebHost.cs**  
```c#:src/DefaultBuilder/src/WebHost.cs
if (args != null)
{
    builder.UseConfiguration(new ConfigurationBuilder().AddCommandLine(args).Build());
}
```  
  
## 設定読み込み  
  
環境に応じて、設定ファイルを読み込むdelegateを`WebHostBuilder`に渡しています。  
  
デフォルトでは`appsettings.json`ファイルを読み込みます。  
さらに`appsettings.{環境}.json`ファイルを読み込みます。  
  
`Development`環境のときは、追加のアセンブリを読み込んでいます。[^1]  
  
[^1]: 環境名は「Development」「Production」「Staging」と決め打ちです。なかなか厄介。  
  
**src/DefaultBuilder/src/WebHost.cs**  
```c#:src/DefaultBuilder/src/WebHost.cs
builder.ConfigureAppConfiguration((hostingContext, config) =>
{
    var env = hostingContext.HostingEnvironment;

    config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, reloadOnChange: true);

    if (env.IsDevelopment())
    {
        var appAssembly = Assembly.Load(new AssemblyName(env.ApplicationName));
        if (appAssembly != null)
        {
            config.AddUserSecrets(appAssembly, optional: true);
        }
    }

    config.AddEnvironmentVariables();

    if (args != null)
    {
        config.AddCommandLine(args);
    }
})
```  
  
## ロガーを設定  
  
ロガーを設定しています。  
  
****  
```c#:
.ConfigureLogging((hostingContext, logging) =>
// （省略）
)
```  
  
## デフォルトサービスプロバイダを設定  
  
****  
```c#:
.UseDefaultServiceProvider((context, options) =>
{
    options.ValidateScopes = context.HostingEnvironment.IsDevelopment();
})
```  
  
## もろもろデフォルト設定  
  
### Kestrel webサーバを使う(?)  
  
****  
```c#:
.UseKestrel((builderContext, options) =>
{
    options.Configure(builderContext.Configuration.GetSection("Kestrel"));
})
```  
  
ソースファイル: https://github.com/aspnet/AspNetCore/blob/master/src/Servers/Kestrel/Kestrel/src/WebHostBuilderKestrelExtensions.cs  
  
### DIコンテナに追加  
  
****  
```c#:
.ConfigureServices((hostingContext, services) =>
{
    services.PostConfigure<HostFilteringOptions>(/* （省略） */);

    services.AddSingleton<IOptionsChangeTokenSource<HostFilteringOptions>>(/* （省略） */);

    services.AddTransient<IStartupFilter, HostFilteringStartupFilter>();

    services.AddRouting();
})
```  
  
### IISを使う(?)  
  
****  
```c#:
.UseIIS()
.UseIISIntegration()
```  
  
ソースファイル: https://github.com/aspnet/AspNetCore/blob/master/src/Servers/IIS/IIS/src/WebHostBuilderIISExtensions.cs  
  
  
# 2行目: `.UseStartup<Startup>()`  
  
DIコンテナにスタートアップクラスを登録するdelegateを`WebHostBuilder`に追加しています。  
  
## 実際にはスタートアップクラスの代わりに`ConventionBasedStartup`クラスを登録  
  
実際には`ConventionBasedStartup`クラスを使用するようになっています。  
  
ソースファイル: https://github.com/aspnet/AspNetCore/blob/master/src/Hosting/Hosting/src/WebHostBuilderExtensions.cs  
  
****  
```c#:
return hostBuilder
    .ConfigureServices(services =>
    {
// (省略)
        services.AddSingleton(typeof(IStartup), sp =>
        {
            var hostingEnvironment = sp.GetRequiredService<IHostingEnvironment>();
            return new ConventionBasedStartup(StartupLoader.LoadMethods(sp, startupType, hostingEnvironment.EnvironmentName));
        });
// (省略)
    });
```  
  
ソースファイル: https://github.com/aspnet/AspNetCore/blob/master/src/Hosting/Hosting/src/Startup/ConventionBasedStartup.cs  
  
## スタートアップクラスの呼び出し先メソッドを決定  
  
`ConverntionBasedStartup`クラスが呼び出すメソッドは、`StartupLoader`で決めています。  
  
ソースファイル: https://github.com/aspnet/AspNetCore/blob/master/src/Hosting/Hosting/src/Internal/StartupLoader.cs  
  
  
# 3行目: `.Build()`  
  
`WebHost`をビルドします。  
  
ソースファイル: https://github.com/aspnet/AspNetCore/blob/master/src/Hosting/Hosting/src/WebHostBuilder.cs  
  
## 共通サービスの登録  
  
このあたり、なにやっているか追えていません。  
  
****  
```c#:
var services = new ServiceCollection();
// （省略）
services.AddSingleton<IHostingEnvironment>(_hostingEnvironment);
services.AddSingleton<Extensions.Hosting.IHostingEnvironment>(_hostingEnvironment);
services.AddSingleton(_context);

// （省略）

var configuration = builder.Build();
services.AddSingleton<IConfiguration>(configuration);
_context.Configuration = configuration;

var listener = new DiagnosticListener("Microsoft.AspNetCore");
services.AddSingleton<DiagnosticListener>(listener);
services.AddSingleton<DiagnosticSource>(listener);

services.AddTransient<IApplicationBuilderFactory, ApplicationBuilderFactory>();
services.AddTransient<IHttpContextFactory, DefaultHttpContextFactory>();
services.AddScoped<IMiddlewareFactory, MiddlewareFactory>();
services.AddOptions();
services.AddLogging();

// （省略）
```  
  
  
## `WebHost`のインスタンスを作成  
  
  
`WebHost`のインスタンスを作成し、`Initialize`を呼んでいます。  
  
  
****  
```c#:
var host = new WebHost(
    applicationServices,
    hostingServiceProvider,
    _options,
    _config,
    hostingStartupErrors);

(snip)
host.Initialize();
```  
  
## `WebHost`の初期化  
  
ソースファイル: https://github.com/aspnet/AspNetCore/blob/master/src/Hosting/Hosting/src/Internal/WebHost.cs  
  
  
### スタートアップクラスの`ConfigureServices`を呼び出し  
  
****  
```c#:
_startup = _hostingServiceProvider.GetService<IStartup>();
```  
  
****  
```c#:
_applicationServices = _startup.ConfigureServices(_applicationServiceCollection);
```  
  
# 4行目: `.Run();`  
  
## `WebHost`の`StartAsync`を呼び出し  
  
ソースファイル: https://github.com/aspnet/AspNetCore/blob/master/src/Hosting/Hosting/src/WebHostExtensions.cs  
  
****  
```c#:
await host.StartAsync(token);
```  
  
ソースファイル: https://github.com/aspnet/AspNetCore/blob/master/src/Hosting/Hosting/src/Internal/WebHost.cs  
  
## アプリケーションのビルド  
  
****  
```c#:
var application = BuildApplication();
```  
  
### サーバの作成  
  
****  
```c#:
Server = _applicationServices.GetRequiredService<IServer>();
```  
  
### アドレスの追加(かな?)  
  
****  
```c#:
var urls = _config[WebHostDefaults.ServerUrlsKey] ?? _config[DeprecatedServerUrlsKey];
// （省略）
foreach (var value in urls.Split(new[] { ';' }, StringSplitOptions.RemoveEmptyEntries))
{
    addresses.Add(value);
}
```  
  
### `ApplicationBuilder`を作成  
  
****  
```c#:
var builderFactory = _applicationServices.GetRequiredService<IApplicationBuilderFactory>();
var builder = builderFactory.CreateBuilder(Server.Features);
builder.ApplicationServices = _applicationServices;
```  
  
ソースファイル: https://github.com/aspnet/AspNetCore/blob/master/src/Hosting/Hosting/src/Builder/ApplicationBuilderFactory.cs  
  
****  
```c#:
return new ApplicationBuilder(_serviceProvider, serverFeatures);
```  
  
### フィルタクラス達を呼び出し（かな？）  
  
  
****  
```c#:
var startupFilters = _applicationServices.GetService<IEnumerable<IStartupFilter>>();
Action<IApplicationBuilder> configure = _startup.Configure;
foreach (var filter in startupFilters.Reverse())
{
    configure = filter.Configure(configure);
}

configure(builder);
```  
  
### アプリケーションをビルド  
  
ソースファイル: https://github.com/aspnet/AspNetCore/blob/master/src/Http/Http/src/Internal/ApplicationBuilder.cs  
  
`_components`には、useしたdelegateが入っています。  
  
****  
```c#:
// （省略）
foreach (var component in _components.Reverse())
{
    app = component(app);
}

return app;
```  
  
  
## `HostingApplication`インスタンスの作成  
  
****  
```c#:
var diagnosticSource = _applicationServices.GetRequiredService<DiagnosticListener>();
var httpContextFactory = _applicationServices.GetRequiredService<IHttpContextFactory>();
var hostingApp = new HostingApplication(application, _logger, diagnosticSource, httpContextFactory);
```  
  
## サーバ起動  
  
****  
```c#:
await Server.StartAsync(hostingApp, cancellationToken).ConfigureAwait(false);
```  
  
  
  
  
