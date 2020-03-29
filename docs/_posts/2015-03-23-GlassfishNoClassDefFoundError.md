---
'created_at: ': '2015-03-23T14:04:50+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Java
      versions: []
    - name: glassfish
      versions:
      - '4.1'
  state: 0
title: "Glassfish\u3067NoClassDefFoundError\u304C\u3067\u308B\u3068\u304D\u306B\u30C1\
  \u30A7\u30C3\u30AF\u3059\u3079\u304D\u3053\u3068"
'updated_at: ': '2015-03-23T14:04:50+09:00'

---
Glassfishにアプリケーションをデプロイしたけど、NoClassDefFoundErrorが出て動かん！なんでやねん！このこんぴゅーたおかしいわ！  
なんてつぶやいてしまうときにチェックすべきことです。  
  
# 環境  
  
- Glassfish 4.1  
  
# チェック項目  
  
0. 名前が違う  
一番プリミティブな問題です。その名前のクラスが存在するかチェックします。  
  
0. *ドメインディレクトリ*/lib/に余計なjarがある  
*ドメインディレクトリ*/lib/にあるクラスが他クラスに依存していて、そのクラスが*ドメインディレクトリ*/lib/に存在しないとエラーになります。余計なjarがないかチェックします。  
