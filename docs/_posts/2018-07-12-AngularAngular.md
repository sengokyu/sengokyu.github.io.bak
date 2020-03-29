---
'created_at: ': '2018-07-12T12:08:47+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Chrome
      versions: []
    - name: firefox
      versions: []
    - name: Angular
      versions: []
  state: 0
title: "[Angular] Angular\u30A2\u30D7\u30EA\u3092\u30D6\u30E9\u30A6\u30B6\u306E\u30B3\
  \u30F3\u30BD\u30FC\u30EB\u304B\u3089\u30C7\u30D0\u30C3\u30B0\u3059\u308B\u65B9\u6CD5"
'updated_at: ': '2018-07-12T12:18:04+09:00'

---
Angularのコンポーネントを、ブラウザのコンソールからちょっと覗いてみる方法です。  
  
AngularJS時代は、[angular.element($0).scope()](2015-01-29-AngularJSJavaScriptAngularJS.md)なんて方法でいけました。それのAngular時代版です。  
  
# 見たいエレメントを選ぶ  
  
インスペクタから見たいエレメントを選びます。  
  
![image.png](/assets/images/6133c83a-f28b-001d-6175-a3b410bf57a0.png)  
  
  
# コンソールへ`ng.probe`と入力  
  
次のように入力すると、コンポーネントオブジェクトがダンプできます。  
  
****  
```js:
ng.probe($0).componentInstance
```  
![image.png](/assets/images/7f7d739d-1104-10c9-9a13-4d7d6b4154bc.png)  
  
  
何かエラーが出たら、間違ったエレメントを選んだか、デバッグモードが無効になっていると思われます。  
