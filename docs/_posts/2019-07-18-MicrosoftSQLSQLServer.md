---
date: '2019-07-18T23:25:24+09:00'
layout: post
published: true
qiita_article_id: d628d0ac50ef54da4268
tags:
- SQLServer
title: '[Microsoft] SQLから始めるSQL Serverデータベース'
updated: '2020-04-02T10:25:11+09:00'

---
# これはなに？  
  
GUIではなくSQLでSQL Serverを使い始めた時の個人的なメモです。  
  
  
# 前書き  
  
Windowsであれば、SQL Server Management Studio(SSMS)という便利なGUIがあります。  
LinuxやmacOS用には、[Azure Data Studio](https://docs.microsoft.com/ja-jp/sql/azure-data-studio/download)というGUIがあります。  
Azure Data Studioはまだ発展途上なので、SSMSほど機能がありません。代わりにSQLを使う必要があります。  
  
# SQL Server の始め方  
  
## データベース作成  
  
```sql
CREATE DATABASE データベース名 COLLATE 比較条件
go
```  
  
## ユーザー作成  
  
### ログイン情報作成  
  
まずSQL Serverへ接続するための情報を作成します。  
  
```sql
CREATE LOGIN ログインID
WITH
  PASSWORD = 'パスワード',
  DEFAULT_DATABASE = データベース名,
  CHECK_EXPIRATION = OFF, -- 有効期限チェックしない
  CHECK_POLICY = OFF -- パスワードの複雑性要件をチェックしない
go
```  
  
### データベースユーザー作成  
  
データベースにログイン情報と一致するユーザーを作成します。  
  
```sql
use 「データベース]
go
CREATE USER ログインID
go
```  
  
### ロール割り当て  
  
ロールを割り当てます。  
  
```sql
use [データベース]
go
EXEC sp_addrolemember 'db_owner', 'ログインID'
go
```  
  
## パスワード変更  
  
```sql
ALTER LOGIN ログインID
WITH
  PASSWORD = '新パスワード'
GO
```  
