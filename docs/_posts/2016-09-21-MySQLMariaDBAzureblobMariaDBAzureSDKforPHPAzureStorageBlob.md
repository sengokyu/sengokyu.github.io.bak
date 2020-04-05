---
date: '2016-09-21T14:36:22+09:00'
layout: post
published: true
qiita_article_id: 568f6d93841babaaf5a0
tags:
- PHP
- MySQL
- Azure
- mariadb
title: '[MySQL][MariaDB][Azure][blob] MariaDBのダンプ（バックアップ）をAzure SDK for PHPを使ってAzure
  Storage Blobへ保存する＆悲報'
updated: '2016-09-21T14:38:50+09:00'

---
# 書いてあること  
  
cron.dailyでMySQLのダンプをとり、それをAzure Storage Blobへアップロードします。  
  
アップロードは、Linux用 [Azure CLI](https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/) もあるのですが、あえてPHPの自前スクリプトにしました。  
node.jsをインストールするのも面倒だったので。  
  
  
# 準備  
  
## Azure Storage Accountの作成  
  
[Azure Portal](https://portal.azure.com/) を使ってStorage Accountを作成します。  
  
構成は次のようにしました。  
  
| 構成項目 | 設定値 |  
|:-:|:-:|  
| Deployment Model   | Resource Manager   |  
| Account kind  | Blob storage   |  
| Performance | Standard |  
| Replication | LRS[^1] |  
| Access Tier | Cool[^2] |  
  
[^1]: ローカルのデータセンターのみの冗長化です。他にジオ冗長とかあります。  
[^2]: 頻繁にアクセスがあるものは「Hot」、あまりアクセスがないものは「Cool」にします。  
  
デプロイが終わったら、名前とAccess Keyをメモしておきます。  
  
## ストレージコンテナの作成  
  
[Azure Storage Explorer](http://storageexplorer.com/) を使用して、ストレージアカウントの中にコンテナを作成します。  
  
作成した名前はメモしておきます。  
  
  
## PHP SDK for Azureのダウンロード  
  
composerを使ってダウンロードします。  
  
```
$ php composer.phar require microsoft/windowsazure
Your version of PHP, 5.4.16, is affected by CVE-2013-6420 and cannot safely perform certificate validation, we strongly suggest you upgrade.
Using version ^0.4.2 for microsoft/windowsazure
./composer.json has been created
Loading composer repositories with package information
Updating dependencies (including require-dev)
  - Installing firebase/php-jwt (v3.0.0)
    Downloading: 100%         

  - Installing pear/pear_exception (v1.0.0)
    Downloading: 100%         

  - Installing pear/mail_mime-decode (1.5.5.2)
    Downloading: 100%         

  - Installing pear/console_getopt (v1.4.1)
    Downloading: 100%         

  - Installing pear/pear-core-minimal (v1.10.1)
    Downloading: 100%         

  - Installing pear/mail_mime (1.10.0)
    Downloading: 100%         

  - Installing pear/net_url2 (v2.2.1)
    Downloading: 100%         

  - Installing pear/http_request2 (v2.3.0)
    Downloading: 100%         

  - Installing microsoft/windowsazure (v0.4.2)
    Downloading: 100%         

Writing lock file
Generating autoload files
```  
  
CentOS 7なので、PHPのバージョン警告が出ますが無視です。 ;)  
  
  
  
# スクリプト  
  
## ファイルをアップロードするスクリプト  
  
*youraccountname*、*youraccesskey*、*yourcontainername*のところは、適宜書き換えてください。  
  
`/usr/local/bin/` かどこかお好みの場所に配置します。  
  
**upload-to-azure.php**  
```php:upload-to-azure.php
# !/usr/bin/env php
<?php

namespace UploadToAzure;

ini_set('display_errors', 'on');
ini_set('display_startup_errors', 'on');

### configuration

$proto = 'https';
$accountName = 'youraccountname';
$accessKey = 'youraccesskey';
$containerName = 'yourcontainername';

### end of configuration

require dirname(__DIR__) . '/vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

if ($argc == 1) {
  file_put_contents('php://stderr', "Usage: upload-to-azure.php filename...\n");  exit;
}

$conn = sprintf('DefaultEndpointsProtocol=%s;AccountName=%s;AccountKey=%s',
        $proto, $accountName, $accessKey);

$blobService = ServicesBuilder::getInstance()->createBlobService($conn);

foreach (array_slice($argv, 1) as $arg) {
  $name = basename($arg);

  $fh = fopen($arg, 'r');
  $blobService->createBlockBlob($containerName, $name, $fh);
  fclose($fh);
}

```  
  
## cronスクリプト  
  
`/etc/cron.daily` に配置します。  
  
**/etc/cron.daily/dump-mysql**  
```bash:/etc/cron.daily/dump-mysql
# !/bin/sh

date=`LANG=C date +%Y%m%d`
dest="/var/backup/mysql-${date}.bz2"

command="/usr/bin/mysqldump"
command_args="-u root --all-databases --single-transaction --flush-logs"

if [ -e ${dest} ]; then
  echo Destination ${dest} already exists.
  exit 1
fi

${command} ${command_args} | bzip2 -c > ${dest}
upload-to-azure.php ${dest}
```  
  
# :scream: :scream: :scream: 【悲報】 PHP 5.4.16では、Azure SDK動きません  
  
ここまできておいてなんですが、CentOS 7付属のPHPでは動かないということです。。。  
  
実行すると、こんなエラーになります。  
  
****  
```text:
Fatal error: Uncaught exception 'WindowsAzure\Common\ServiceException' with message 'Fail:
Code: 400
Value: The value for one of the HTTP headers is not in the correct format.
details (if any): ﻿<?xml version="1.0" encoding="utf-8"?><Error><Code>InvalidHeaderValue</Code><Message>The value for one of the HTTP headers is not in the correct format.
RequestId:6f28991f-0001-0039-75c9-13f2d2000000
Time:2016-09-21T05:30:24.6414256Z</Message><HeaderName>x-ms-version</HeaderName><HeaderValue>2012-02-12</HeaderValue></Error>.' in /home/pico/work/mysql-to-blob/vendor/microsoft/windowsazure/WindowsAzure/Common/Internal/Http/HttpClient.php:383
Stack trace:
# 0 /home/pico/work/mysql-to-blob/vendor/microsoft/windowsazure/WindowsAzure/Common/Internal/Http/HttpClient.php(277): WindowsAzure\Common\Internal\Http\HttpClient::throwIfError(400, 'The value for o...', '???<?xml versio...', Array)
# 1 /home/pico/work/mysql-to-blob/vendor/microsoft/windowsazure/WindowsAzure/Common/Internal/RestProxy.php(141): WindowsAzure\Common\Internal\Http\Http in /home/pico/work/mysql-to-blob/vendor/microsoft/windowsazure/WindowsAzure/Common/Internal/Http/HttpClient.php on line 383
```  
  
そんなバージョン指定のこと書いてあったかなぁ。。。  
  
