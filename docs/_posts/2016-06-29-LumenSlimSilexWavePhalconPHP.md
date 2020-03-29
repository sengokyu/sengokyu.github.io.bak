---
'created_at: ': '2016-06-29T11:38:15+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: PHP
      versions: []
    - name: slim
      versions: []
    - name: Silex
      versions: []
    - name: Phalcon
      versions: []
    - name: lumen
      versions: []
  state: 0
title: "[Lumen][Slim][Silex][Wave][Phalcon] PHP\u30DE\u30A4\u30AF\u30ED\u30D5\u30EC\
  \u30FC\u30E0\u30EF\u30FC\u30AF\u6BD4\u8F03"
'updated_at: ': '2016-07-07T12:28:17+09:00'

---
PerlのAmon2みたいな、手軽なフレームワークが欲しくて探してみました。  
  
https://blog.appdynamics.com/php/php-microframework-vs-full-stack-framework/ からの引用です。  
  
|  | PHPバージョン | PHP 7サポート |プロジェクトWebページ | requireパッケージ | クラス数 |  
|:-:|:-:|:-:|:-:|:--|--:|   
| Lumen  | 5.5.9～ | :o: | https://lumen.laravel.com/  | symfony/process<br>symfony/polyfill-mbstring<br>symfony/console<br>guzzlehttp/promises<br>psr/http-message<br>guzzlehttp/psr7<br>guzzlehttp/guzzle | 11[^1] |  
| Slim 3 | 5.5～ | :o: | http://www.slimframework.com/  | pimple/pimple<br>psr/http-message<br>nikic/fast-route<br>container-interop/container-interop | 34 |  
| Slim 2 | 5.3～  | :x: なし？ | http://docs.slimframework.com/  | | |  
| Silex 2| 5.5～ | :o: （2016年6月時点では作業中？）| http://silex.sensiolabs.org/ | symfony/routing<br>symfony/polyfill-mbstring<br>symfony/http-foundation<br>symfony/event-dispatcher<br>psr/log<br>symfony/debug<br>symfony/http-kernel<br>pimple/pimple | 46 |  
| Wave | 5.3～ | :x: なし？| http://www.waveframework.com/ | | |  
| Phalcon | 5.3～ | :o: （2016年6月時点ではベータ扱い） | https://phalconphp.com/en/ | - | - |  
  
[^1]: アプリケーション作成後のvendor/laravel/lumen-frameworkにあるクラス数  
  
# インストール方法  
  
## Lumen  
  
composerを使います。  
  
```
php composer.phar require laravel/lumen-installer
```  
  
## Slim 3  
  
composerを使います。  
  
```
php composer.phar require slim/slim "^3.0"
```  
## Slim 2  
  
composerを使います。  
  
```
php composer.phar require slim/slim "~2.0"
```  
  
## Silex  
  
composerを使います。  
  
```
php composer.phar require silex/silex "~2.0"
```  
  
## Phalcon  
  
PHP Extentionなので、OSによりインストール方法が違います。  
  
https://phalconphp.com/en/download  
  
# 考察  
  
Lumen単独では小さいのですが、いろいろ外部のものを使用しているので、あまりマイクロフレームワークっぽくないかもです。  
  
Waveは開発終了したのでしょうか？更新されていません。  
  
CentOS 6のようなPHP 5.3環境では、Slimi 2がよさそうです。  
