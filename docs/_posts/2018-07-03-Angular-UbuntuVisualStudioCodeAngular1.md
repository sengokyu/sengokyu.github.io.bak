---
'created_at: ': '2018-07-03T17:47:00+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Ubuntu
      versions: []
    - name: TypeScript
      versions: []
    - name: Angular
      versions: []
    - name: VisualStudioCode
      versions: []
  state: 0
title: "[Angular] \u958B\u767A\u74B0\u5883\u6E96\u5099 - Ubuntu\u3068Visual Studio\
  \ Code\u3067\u59CB\u3081\u308BAngular (1)"
'updated_at: ': '2018-07-11T18:46:48+09:00'

---
# はじめに  
  
[Angularのチュートリアル](https://angular.io/tutorial)を写経していきます。  
  
しばらくお目汚し失礼します。  
  
# 前提  
  
環境は以下のとおりです。  
  
* Ubuntu 18.04  
* Visual Studio Code 1.24.1  
  
# 開発環境の準備  
  
~~TypeScriptをインストールします。~~ __(追記) TypeScriptのバージョン指定があるので、グローバルに入れないほうがよいです。__  
  
```shell_session
sudo npm install --global typescript
```  
  
Angularのコマンドをインストールします。  
  
```shell-session
sudo npm install --global @angular/cli
```  
  
## Visual Studio Code 拡張  
  
何を入れればよいか、よくわからないところではあります。  
  
* TypeScript Hero<br>`import`を整理してくれます。  
* Prettier<br>コードフォーマッタです。  
* TSLint<br>コードチェッカーです。VSCode単体でもチェックしてくれるので不要かも?初期状態だとPrettierと喧嘩しますし。。。  
  
  
  
