---
'created_at: ': '2015-07-16T13:30:51+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Angular
      versions: []
    - name: ng-inspector
      versions: []
  state: 0
title: "[AngularJS][JavaScript] AngularJS\u30A2\u30D7\u30EA\u958B\u767A\u3092\u697D\
  \u306B\u3059\u308B\u30D6\u30E9\u30A6\u30B6\u62E1\u5F35\u300Cng-inspector\u300D"
'updated_at: ': '2016-08-09T09:08:12+09:00'

---
以前にこんな記事を書きました。  
[AngularJSアプリをブラウザのコンソールからデバッグする方法](http://qiita.com/sengoku/items/02029aad29cd7f5744bb)  
  
いよいよコンソールからキーを打ち込むのがだるくなってきまして、何かないかと探したらとてもよいブラウザ拡張がありました。  
  
[ng-inspector](http://ng-inspector.org/)です。  
  
これをインストールすれば、ボタン一発でスコープの中身が見えます。  
Chrome/Firefox/Safariに対応しています。すばらしい。  
  
----  
  
**2015/8/18追記**  
  
Firefox 42(?)から、未署名のアドオンはインストールできなくなりました。  
  
[about:config](about:config)を開いて、xpinstall.signatures.requiredをfalseにするとインストールできます。  
  
----  
  
使い方は簡単です。  
[ng-inspector](http://ng-inspector.org/)へアクセスしてインストールしたら、メニューに追加されたボタンを一発押すだけです。  
![ng-inspector1.JPG](/assets/images/d92ee9a4-91af-4c77-b024-c3833b79f00a.jpeg)  
  
そうすると、ずらっとスコープのツリーが表示されます。  
![ng-inspector2.JPG](/assets/images/a5aba435-b611-8ef4-1d94-d166e9af3c81.jpeg)  
  
いえーい。 :laughing:   
楽ちん楽ちん。 :satisfied:   
  
  
