---
date: '2015-03-23T14:04:50+09:00'
layout: post
published: true
qiita_article_id: 9359825d964ab7401536
tags:
- Java
- glassfish
title: GlassfishでNoClassDefFoundErrorがでるときにチェックすべきこと
updated: '2015-03-23T14:04:50+09:00'

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
