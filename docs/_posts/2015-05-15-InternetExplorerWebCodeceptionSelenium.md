---
date: '2015-05-15T00:21:56+09:00'
layout: post
published: true
qiita_article_id: 2edfc9742db1f1d9d0d4
tags:
- PHP
- Selenium
- codeception
- InternetExplorer
title: Internet ExplorerでWebアプリをテストする。CodeceptionとSeleniumを使って。【準備編】
updated: '2015-05-15T13:26:22+09:00'

---
# :one: 環境  
  
- Windows 8.1 x64 en (Internet Explorerを動かすため)  
- CentOS 6 (Webアプリを動かすため)  
- PHP 5.3.3 (CentOS 6付属)  
  
  
# :two: あらかじめ必要なもの   
  
## [Composer](https://getcomposer.org/)  
  
PHPのパッケージマネージャです。Codeceptionをダウンロードするのに使用します。  
  
## Java runtime  
  
Selenium ServerはJavaでできています。  
  
https://java.com/ja/download/ からJava runtimeをダウンロードしてインストールします。  
  
## Selenium ServerとIE Driver  
  
http://docs.seleniumhq.org/download/ から、Selenium ServerとThe Internet Explorer Driver Serverをダウンロードして同じディレクトリに置きます。ZIPは展開してください。  
  
IE Driverは32ビットと64ビットの2つがありますが、どちらでも動くみたいです。  
  
ちなみにJavaは64ビットをインストールしました。  
  
# :three: 準備  
  
## Selenium Serverを起動  
  
Selenium Serverのjarファイルを置いたディレクトリで、次のようにして起動します。  
  
****  
```bat:
%JAVA_HOME%\bin\java -jar selenium-server-standalone-2.45.0.jar -Dwebdriver.ie.driver=./IEDriverServer.exe
```  
  
## codeceptionをダウンロード  
  
``composer.json``の``require-dev``セクションに追加します。PHP 5.3.3なので、古いバージョンを使っています。  
  
**composer.json**  
```json:composer.json
{
    "require-dev": {
        "codeception/codeception": "~1.8.0"
    }
}
```  
  
composerを実行します。  
  
****  
```sh:
./vendor/bin/composer update
```  
  
## codeception用のファイルを生成  
  
設定ファイルやテストを置くディレクトリを作ったりします。  
  
****  
```sh:
./vendor/bin/codecept bootstrap
```  
  
# :four: お試し  
  
とりあえず動くかどうかを試してみます。  
  
## テストを生成  
  
****  
```sh:
./vendor/bin/codecept generate:cept acceptance Welcome
```  
  
## テストを設定  
  
yamlファイルを編集します。  
  
**tests/acceptance.suite.yml**  
```yaml:tests/acceptance.suite.yml
class_name: WebGuy
modules:
    enabled:
        - WebDriver
        - WebHelper
    config:
        WebDriver:
            url: '*テストしたいWebアプリのURL*'
            browser: 'internet explorer'
            host: '*WindowsのIPアドレスかホスト名*'
            port: 4444
            window_size: 1024x800
            wait: 10
```  
  
WebDriverモジュールの設定内容はこのあたり。 http://codeception.com/docs/modules/WebDriver  
  
## テストを編集  
  
**tests/acceptance/WelcomeCept.php**  
```php:tests/acceptance/WelcomeCept.php
<?php
$I = new WebGuy($scenario);
$I->wantTo('See top page');
$I->amOnPage('/');
$I->seeInTitle('Top Page');
```  
  
CodeceptionはDSLっぽくていい感じっす。  
  
  
## テストを実行  
  
****  
```sh:
./vendor/bin/codecept run
```  
  
うまくいけば、ぞぞぞっとIEが起動して画面表示して終了します。  
  
# :five: 動かない……。 :confounded:   
  
## PHPの問題 :open_mouth:   
  
すでに宣言されたメソッドを再宣言しようとしてエラーになっています。もしかすると5.4以降なら問題ないのかな？  
  
****  
```text:
PHP Fatal error:  Can't inherit abstract function WebDriverSearchContext::findElement() (previously declared abstract in WebDriver) in /misc/home/y-okumura/projects/nikkeir-portal/vendor/facebook/webdriver/lib/WebDriver.php on line 19
```  
  
仕方がないのでファイルを修正します。修正するファイルは``vendor/facebook/webdriver/lib/WebDriver.php``です。  
  
****  
```diff:
--- WebDriver.php.orig  2015-05-15 00:07:27.000000000 +0900
+++ WebDriver.php       2015-05-15 00:07:33.000000000 +0900
@@ -33,7 +33,7 @@
    *    HttpCommandExecutor if no element is found.
    * @see WebDriverBy
    */
-  public function findElement(WebDriverBy $locator);
+  //public function findElement(WebDriverBy $locator);

   /**
    * Find all WebDriverElements within the current page using the given
@@ -44,7 +44,7 @@
    *    nothing matches
    * @see WebDriverBy
    */
-  public function findElements(WebDriverBy $locator);
+  //public function findElements(WebDriverBy $locator);

   /**
    * Load a new web page in the current browser window.
```  
  
## IEの問題 :expressionless:   
  
Windows側で例外が起きていました。  
  
****  
```text:
org.openqa.selenium.NoSuchWindowException: Unable to get browser (WARNING: The server did not provide any stacktrace information)
```  
  
### 全ゾーンのプロテクトモード設定をそろえる  
  
https://code.google.com/p/selenium/wiki/InternetExplorerDriver#Required_Configuration によりますと、全ゾーンのプロテクトモード設定を同じにしろとのことです。  
  
![ieoptions.png](/assets/images/b081e540-c38f-326d-ff08-7d20bda1c9ec.png)  
  
Local IntranetゾーンとTrusted Sitesゾーンのプロテクトモードをオンにしました。  
  
とりあえずはこれで動くようになりました。  
  
### Enhancedプロテクトモードを無効にする  
  
Enterprise Editionのせいか画面から設定できなかったので、レジストリを編集しました。  
  
****  
```registry:
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\Internet Explorer\Main]
"Isolation"="PMIL"
```  
  
  
### IEのFeatureを有効？無効？にする :confused:   
  
IEのFeatureをなにか有効？無効？にしました。[MSDN](https://msdn.microsoft.com/en-us/library/ee330720%28v=vs.85%29.aspx)に説明が見つからないのです。  
  
****  
```registry:
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Internet Explorer\MAIN\FeatureControl\FEATURE_BFCACHE]
"iexplore.exe"=dword:00000000
```  
  
  
# :six: つづく  
  
長くなりましたので、続きはまた。  
