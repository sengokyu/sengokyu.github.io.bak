---
'created_at: ': '2017-01-06T10:36:46+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Java
      versions: []
    - name: glassfish
      versions: []
    - name: JavaEE
      versions: []
  state: 0
title: "[Java][Java EE][Glassfish] (\u7D9A)Glassfish\u306E\u30AD\u30E3\u30C3\u30B7\
  \u30E5\u3092\u7591\u3063\u3066\u3044\u304F\u30B9\u30BF\u30A4\u30EB"
'updated_at: ': '2017-01-06T10:38:52+09:00'

---
[Glassfishのキャッシュを疑っていくスタイル](http://qiita.com/opengl-8080/items/838758ea34efcb0ea958)を真似っ子して、自分もGlassfishのキャッシュを疑っていくスタイルで生きていこうという宣言です。  
  
Glassfishを使っていると、こんな症状が出ることがあります。  
  
- アプリをデプロイしなおしたら、なんでかエラーで動かない。  
- JDBCリソース追加したら、Glassfishが起動しない。  
- エラーが出た。 `RAR7132: Unable to enlist the resource in transaction. Returned resource to pool.`  
- エラーが出た。 `RAR5117 : Failed to obtain/create connection from connection pool [ ... ]. Reason : com.sun.appserv.connectors.internal.api.PoolingException: java.lang.IllegalStateException: Local transaction already has 1 non-XA Resource: cannot add more resources.  ]]`  
- エラーが出た。 `RAR5114 : Error allocating connection : [Error in allocating a connection. Cause: java.lang.IllegalStateException: Local transaction already has 1 non-XA Resource: cannot add more resources.  ]]]`  
  
そんなときは、このスタイルでいきましょう。   
↓  
[Glassfishのキャッシュを疑っていくスタイル](http://qiita.com/opengl-8080/items/838758ea34efcb0ea958)  
  
でもきっと、Payaraへ移行するのが最も正解に近い。  
