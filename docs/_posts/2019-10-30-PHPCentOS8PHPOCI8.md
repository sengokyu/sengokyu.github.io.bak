---
date: '2019-10-30T14:53:35+09:00'
layout: post
published: true
qiita_article_id: 16c12b459dddf3a14e19
tags:
- PHP
- CentOS
- oracle
title: '[PHP] CentOS 8にPHPとOCI8をインストールするメモ'
updated: '2019-10-30T15:59:51+09:00'

---
# はじめに  
  
CentOS 8にPHPとPHPからOracle接続するための拡張をインストールします。  
  
# 手順  
  
## Oracle Instant Client をインストール  
  
https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html から RPM をダウンロードします。  
  
BASIC パッケージと SDK パッケージです。  
  
```console
sudo rpm -Uvh oracle-instantclient19.3-basic-19.3.0.0.0-1.x86_64.rpm
sudo rpm -Uvh oracle-instantclient19.3-devel-19.3.0.0.0-1.x86_64.rpm
```  
  
クライアントバージョンをどれにするかは、Oracleのサポートドキュメントを参照してください。  
  
https://www.oracle.com/database/technologies/faq-instant-client.html  
  
> An Instant Client based application can interoperate with any database version that the full Oracle Client interoperates with. For example, Instant Client 12.2 allows connection to 11.2, 12.1 and 12.2 databases. Details are in the interoperability matrix in Support Doc ID 207303.1 Note that Instant Client includes a number of different Oracle tools and technologies, some of which may have more restrictive requirements.  
  
  
## 必要なものをインストール  
  
```console
sudo dnf -y install php
sudo dnf -y install php-pear
# peclでのビルドに必要
sudo dnf -y install php-devel
sudo dnf -y install make
```  
  
## OCI8をインストール  
  
peclでいっぱつです。  
途中Oracleクライアントの場所を聞かれます。autodetectで大丈夫でした。  
  
```console
sudo pecl install oci8
# Oracleのパスはautodetectで大丈夫です。
```  
  
`/etc/php.d/30-oci8.ini`ファイルを作成します。  
  
**/etc/php.d/30-oci8.ini**  
````ini:/etc/php.d/30-oci8.ini
; Enable OCI8 pear package
extension=oci8
```  
  
## libnslをインストール  
  
OCI8が必要としていました。  
  
```console
sudo dnf -y install libnsl
````  
  
# お試し  
  
できたかどうか、以下のようなPHPを実行してみるのが簡単です。  
  
```php
<?php
$oci = oci_connect('ユーザ', 'パスワード', '簡易接続文字列');
$s = oci_parse($oci, 'SELECT SYSDATE FROM DUAL');
oci_execute($s);
oci_fetch_all($s, $r);
print_r($r);
oci_close($oci);

```  
  
  
# トラブルシュート  
  
`/usr/lib64/php/modules/oci8.so` は存在するのに、ファイルが見つからないというWarningが出力される。  
  
oci8.soが必要としているライブラリがありません。  
  
```
ldd /usr/lib64/php/modules/oci8.so
```  
  
として、必要なライブラリを確認します。  
  
# メモ  
  
以下はメモ書きです。  
何かのご参考になれば。  
  
## Oracle Instant Client Basicパッケージでインストールされるもの  
  
```console
$ rpm -ql oracle-instantclient19.3-basic-19.3.0.0.0-1.x86_64
/etc/ld.so.conf.d/oracle-instantclient.conf
/usr/lib/oracle
/usr/lib/oracle/19.3
/usr/lib/oracle/19.3/client64
/usr/lib/oracle/19.3/client64/bin
/usr/lib/oracle/19.3/client64/bin/adrci
/usr/lib/oracle/19.3/client64/bin/genezi
/usr/lib/oracle/19.3/client64/lib
/usr/lib/oracle/19.3/client64/lib/libclntsh.so
/usr/lib/oracle/19.3/client64/lib/libclntsh.so.10.1
/usr/lib/oracle/19.3/client64/lib/libclntsh.so.11.1
/usr/lib/oracle/19.3/client64/lib/libclntsh.so.12.1
/usr/lib/oracle/19.3/client64/lib/libclntsh.so.18.1
/usr/lib/oracle/19.3/client64/lib/libclntsh.so.19.1
/usr/lib/oracle/19.3/client64/lib/libclntshcore.so
/usr/lib/oracle/19.3/client64/lib/libclntshcore.so.19.1
/usr/lib/oracle/19.3/client64/lib/libipc1.so
/usr/lib/oracle/19.3/client64/lib/libmql1.so
/usr/lib/oracle/19.3/client64/lib/libnnz19.so
/usr/lib/oracle/19.3/client64/lib/libocci.so.19.1
/usr/lib/oracle/19.3/client64/lib/libociei.so
/usr/lib/oracle/19.3/client64/lib/libocijdbc19.so
/usr/lib/oracle/19.3/client64/lib/liboramysql19.so
/usr/lib/oracle/19.3/client64/lib/network
/usr/lib/oracle/19.3/client64/lib/network/admin
/usr/lib/oracle/19.3/client64/lib/network/admin/README
/usr/lib/oracle/19.3/client64/lib/ojdbc8.jar
/usr/lib/oracle/19.3/client64/lib/xstreams.jar
/usr/share/oracle
/usr/share/oracle/19.3
/usr/share/oracle/19.3/client64
/usr/share/oracle/19.3/client64/doc
/usr/share/oracle/19.3/client64/doc/BASIC_LICENSE
/usr/share/oracle/19.3/client64/doc/BASIC_README
```  
  
  
