---
date: '2018-07-03T17:47:00+09:00'
layout: post
published: true
qiita_article_id: 2470dfa4b749571d5d68
tags:
- Ubuntu
- TypeScript
- Angular
- VisualStudioCode
title: '[Angular] 開発環境準備 - UbuntuとVisual Studio Codeで始めるAngular (1)'
updated: '2018-07-11T18:46:48+09:00'

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
  
  
  
