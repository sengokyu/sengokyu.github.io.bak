---
date: '2015-02-02T19:16:47+09:00'
layout: post
published: true
qiita_article_id: 27999db68108332be979
tags:
- derby
title: '[Java][Derby] Apache Derbyコマンドラインメモ'
updated: '2016-08-19T17:28:56+09:00'

---
# 起動  
  
glassfishに付属しているderbyを使う場合です。  
  
**commandline**  
```bat:commandline
>"\Program Files\glassfish-4.1\javadb\bin\ij"
```  
  
# データベースへ接続  
  
**commandline**  
```sql:commandline
ij> connect 'jdbc:derby:データベースディレクトリへのパス`;
```  
  
データベースディレクトリへのパスは、相対パスでもOKです。  
  
# スキーマ一覧  
  
**commandline**  
```sql:commandline
ij> show schemas;
```  
  
# テーブル一覧  
  
**commandline**  
```sql:commandline
ij> show tables;
```  
  
スキーマを指定するときは、`in スキーマ名`を付ける。  
  
**commandline**  
```sql:commandline
ij> show tables in app;
```  
  
# テーブル構造を見る  
  
**commandline**  
```sql:commandline
ij> describe テーブル名;
```  
  
あるいは  
  
**commandline**  
```sql:commandline
ij> describe スキーマ名.テーブル名;
```  
  
# テーブルに付いている制約名を取得する  
  
**commandline**  
```sql:commandline
ij> select c.constraintname from sys.systables t, sys.sysconstraints c, sys.syskeys k where t.tableid=c.tableid and t.tablename='テーブル名' and c.constraintid=k.constraintid;
```  
  
  
