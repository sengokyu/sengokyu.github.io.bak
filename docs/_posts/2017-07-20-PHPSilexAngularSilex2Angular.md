---
date: '2017-07-20T20:06:03+09:00'
layout: post
published: true
qiita_article_id: 14d89a2649e50afbc115
tags:
- PHP
- JavaScript
- Silex
- Angular
- Angular2
title: '[PHP][Silex][Angular] Silex 2 + Angular コト始め'
updated: '2017-07-21T09:17:22+09:00'

---
まずは環境を整えるところまでです。  
  
# あらかじめ必要なもの  
  
* PHP  
* composer.phar  
* node.js 6.9以降  
* npm  
  
# 手順  
  
## :footprints: 1 - Silexアプリケーションの雛形を作成  
  
Silexのスケルトンを使います。  
  
```shell-session
% php composer.phar create-project fabpot/silex-skeleton myproj "~2.0"
```  
  
`myproj`フォルダに以下のようなファイルができあがります。  
  
```
.gitignore
LICENSE
README.rst
bin/
  console
composer.json
composer.lock
config/
  dev.php
  prod.php
phpunit.xml.dist
src/
  console.php
  controllers.php
  app.php
templates/
  errors/
    404.html.twig
    4xx.html.twig
    500.html.twig
    5xx.html.twig
    default.html.twig
  index.html.twig
  layout.html.twig
tests/
  controllersTest.php
var/
  cache/
  logs/
vendor/
  （略）
web/
  index.php
  index_dev.php
  css/
    main.css
```  
  
  
## :footprints: 2 Angular CLIをインストール  
  
```shell-session
% npm install -g @angular/cli
```  
  
プロジェクトローカルの`node_modules`に入れて使いたかったのですが、使えないよと怒られるので`-g`つけてインストールします。  
  
  
## :footprints: 3 Angularのプロジェクトを生成  
  
`ng new`コマンドを使います。  
  
```shell-session
% cd myproj
% ng new --skip-git --source-dir web --directory . myproj
```  
  
`--skip-git`オプションで、gitコミットさせないようにします。  
`--source-dir`オプションで、Silexが作成したwebディレクトリを指定します。  
  
  
こんな感じにファイルが作られます。  
  
```
installing ng  
  create .editorconfig                                                                                                                                                             
  create README.md                                                                                                                                                                 
  create web/app/app.component.css                                                                                                                                                 
  create web/app/app.component.html                                                                                                                                                
  create web/app/app.component.spec.ts                                                                                                                                             
  create web/app/app.component.ts                                                                                                                                                  
  create web/app/app.module.ts                                                                                                                                                     
  create web/assets/.gitkeep                                                                                                                                                       
  create web/environments/environment.prod.ts                                                                                                                                      
  create web/environments/environment.ts                                                                                                                                           
  create web/favicon.ico                                                                                                                                                           
  create web/index.html                                                                                                                                                            
  create web/main.ts                                                                                                                                                               
  create web/polyfills.ts                                                                                                                                                          
  create web/styles.css                                                                                                                                                            
  create web/test.ts                                                                                                                                                               
  create web/tsconfig.app.json                                                                                                                                                     
  create web/tsconfig.spec.json
  create web/typings.d.ts
  create .angular-cli.json
  create e2e/app.e2e-spec.ts
  create e2e/app.po.ts
  create e2e/tsconfig.e2e.json
  create karma.conf.js
  create package.json
  create protractor.conf.js
  create tsconfig.json
  create tslint.json
Installing packages for tooling via npm.
Project 'myproj' successfully created.
```  
  
## :footprints: 4 .gitignoreファイルに追記  
  
gitをスキップしたので、手動で追記しておきます。  
  
**.gitignore**  
```text:.gitignore
# See http://help.github.com/ignore-files/ for more about ignoring files.

# compiled output
/dist
/tmp
/out-tsc

# dependencies
/node_modules

# IDEs and editors
/.idea
.project
.classpath
.c9/
*.launch
.settings/
*.sublime-workspace

# IDE - VSCode
.vscode/*
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
!.vscode/extensions.json

# misc
/.sass-cache
/connect.lock
/coverage
/libpeerconnection.log
npm-debug.log
testem.log
/typings

# e2e
/e2e/*.js
/e2e/*.map

# System Files
.DS_Store
Thumbs.db
```  
  
  
## :footprints: 5 PHPビルトインWebサーバを起動  
  
PHP動かします。  
  
```shell-session
% php -S 0.0.0.0:7000 -t web web/index_dev.php
```  
  
ブラウザを立ち上げて http://localhost:7000/ へアクセスします。  
  
