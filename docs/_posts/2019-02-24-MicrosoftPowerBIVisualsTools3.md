---
date: '2019-02-24T22:05:23+09:00'
layout: post
published: true
qiita_article_id: ba0eb5aa6b44c5bc9a69
tags:
- TypeScript
- PowerBI
title: '[Microsoft] PowerBI Visuals Tools バージョン3が出ます'
updated: '2019-02-24T22:05:23+09:00'

---
PowerBI Visuals Tools 3.0 が出ます！  
  
https://microsoft.github.io/PowerBI-visuals/docs/how-to-guide/migrating-to-powerbi-visuals-tools-3-0/  
  
※ 2019年2月時点ではベータ版です。  
  
  
何が変わったのか、気づいた点を挙げていってみます。  
  
# TypeScript関係  
  
## バージョン3になった！  
  
TypeScriptのバージョンが2から3にあがりました。  
嬉しい！  
  
  
## モジュールになった！  
  
2.x時代は`namespace`を使っていましたので、数多ある外部ライブラリを使うのは厄介でした。  
これからは普通に`import`できます。  
  
  
  
## 出力先がディレクトリになった！  
  
今までは`out`オプションで1ファイルにまとめていましたが、バラバラのファイルに出力するようになりました。  
外部ライブラリ使うにはこれがいい。  
  
## ターゲットがES5からES6に！  
  
ES6も指定してOKになりましたよ。  
やったね。  
  
   
# CLI関係  
  
証明書関係のコマンドがなくなった。  
  
`--create-cert`がなくなりました。  
  
  
# 早速使ってみましょう  
  
****  
```console:
$ npm install powerbi-visuals-tools@beta -g
$ pbiview new ExBeta3
$ cd exBeta3
$ pbiviz start

 info   Starting server...
 info   Start preparing plugin template
(node:53190) DeprecationWarning: Tapable.plugin is deprecated. Use new API on `.hooks` instead
 error  ENOENT: no such file or directory, open '/Users/sengoku/Documents/PowerBI/exBeta3/.tmp/precompile/visualPlugin.ts'
(node:53190) UnhandledPromiseRejectionWarning: Error: Failed to generate visualPlugin.ts
    at generateVisualPlugin.then.catch.ex (/usr/local/lib/node_modules/powerbi-visuals-tools/node_modules/powerbi-visuals-webpack-plugin/index.js:168:12)
(node:53190) UnhandledPromiseRejectionWarning: Unhandled promise rejection. This error originated either by throwing inside of an async function without a catch block, or by rejecting a promise which was not handled with .catch(). (rejection id: 1)
(node:53190) [DEP0018] DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.
```  
  
まだ動かないみたいですね。。。  
  
## 環境  
  
- macOS 10.13.6  
- node 11.8  
  
  
