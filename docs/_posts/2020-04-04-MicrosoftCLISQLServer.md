---
date: '2020-04-04T11:59:19+09:00'
layout: post
published: true
qiita_article_id: fe00416bf051864f22aa
tags:
- SQLServer
- Docker
title: '[Microsoft] CLIから始めるSQL Server'
updated: '2020-04-04T11:59:19+09:00'

---
# これはなに？  
  
docker pullしてrunしたはいいけれど、SQL Server Management Studio (通称SSMS)がないゆえに、CLIからSQL Serverを使うことになった男のメモです。  
  
# 接続  
  
```console
sqlcmd -H ホスト名 -U ログインID -P パスワード
```  
  
データベースを指定する場合は`-d データベース名`をつける  
  
例）  
  
```console
sqlcmd -H localhost -U sa -P password2020! -d mydb
```  
  
# SQLファイルを読み込み  
  
```console
sqlcmd -H ホスト名 -U ログインID -P パスワード -i ファイル名
```  
  
ファイルがいくつもある場合は、こんな感じでどうでしょうか。bash/zsh使いのひと用です。  
  
```zsh
for a in *.sql; do
  sqlcmd -H ホスト名 -U ログインID -P パスワード -i "$a"
done
```  
  
`xargs`のほうがいいかもしれません。  
  
```zsh
ls -1 *.sql | xargs -I% sqlcmd -H ホスト名 -U ログインID -P パスワード -i %
```  
  
ファイル名にバージョン番号がついている（例えば、migration-1.0.0.sqlとか）場合は、`sort --version-sort`で並べ直すと幸せになれます。  
  
  
# （おまけ） Dockerから始めるSQL Server  
  
Macのひとは、[Docker Desktop](https://www.docker.com/products/docker-desktop)をインストールします。  
  
[Docker hub](https://hub.docker.com/_/microsoft-mssql-server)を参照して、好みのバージョンのSQL Serverをpullします。  
  
下記は2017を落とす場合です。  
  
```console
docker pull mcr.microsoft.com/mssql/server:2017-latest
```  
  
コンテナを起動します。  
**mssql**という名前をつけました。  
カレントディレクトリを`/mnt`にマウントして、コンテナ内から見えるようにしました。  
  
```console
docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=password2020!' -p 1433:1433 --name mssql -v "$PWD:/mnt" -d mcr.microsoft.com/mssql/server:2017-latest
```  
  
`sqlcmd`コマンドはこのように実行します。  
作業ディレクトリを`/mnt`にしています。  
  
```console
docker exec -it -w /mnt mssql /opt/mssql-tools/bin/sqlcmd
```  
（なんでPATH通ってないんや。。。）  
  
# （おまけその2） SQLから始めるSQL Serverデータベース  
  
データベースを作ったり、ユーザーを作ったりするSQLは別記事に書きました。  
  
[[Microsoft] SQLから始めるSQL Serverデータベース](2019-07-18-MicrosoftSQLSQLServer.md)  
