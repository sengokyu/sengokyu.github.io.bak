---
date: '2017-07-14T17:30:44+09:00'
layout: post
published: true
qiita_article_id: 59ec6e9f694922d2ef5c
tags:
- CentOS
- oVirt
title: '[CentOS][oVirt] oVirt 4.1.1を4.1.3へアップグレード'
updated: '2017-07-14T17:30:44+09:00'

---
CentOS 7上で動いているoVirt 4.1.1を4.1.3へアップグレードしました。  
  
手順としてはリリースノートにあるとおりです。  
http://www.ovirt.org/release/4.1.3/#install--upgrade-from-previous-versions  
  
  
# oVirt Enginのアップグレード  
  
## 手順1  
  
setup関係のものをアップグレード  
  
```shell-session
# yum update "ovirt-*-setup*"
```  
  
## 手順２  
  
`engine-setup`を実行  
  
```shell-session
# engine-setup
```  
  
いくつかわらわらと質問が表示されます。  
  
```shell-session
[ INFO  ] Checking for product updates...
          Setup has found updates for some packages:
(snip)
          do you wish to update them now? (Yes, No) [Yes]:
```  
  
```shell-session
[ INFO  ] Checking for an update for Setup...

          --== NETWORK CONFIGURATION ==--

          Setup can automatically configure the firewall on this system.
          Note: automatic configuration of the firewall may overwrite current settings.
          Do you want Setup to configure the firewall? (Yes, No) [Yes]:
```  
  
```shell-session
          --== DATABASE CONFIGURATION ==--

          The detected DWH database size is 155 MB.
          Setup can backup the existing database. The time and space required for the database backup depend on its size. This process takes time, and in some cases (for instance,
 when the size is few GBs) may take several hours to complete.
          If you choose to not back up the database, and Setup later fails for some reason, it will not be able to restore the database and all DWH data will be lost.
          Would you like to backup the existing database before upgrading it? (Yes, No) [Yes]:
```  
  
```shell-session
          --== OVIRT ENGINE CONFIGURATION ==--

          Perform full vacuum on the engine database engine@localhost?
          This operation may take a while depending on this setup health and the
          configuration of the db vacuum process.
          See https://www.postgresql.org/docs/9.0/static/sql-vacuum.html
          (Yes, No) [No]: Yes
```  
  
```shell-session
[ INFO  ] Stage: Setup validation
          During execution engine service will be stopped (OK, Cancel) [OK]:
```  
  
  
すべて「Yes」と答えて問題ありませんでした。  
  
# ホストのアップグレード  
  
oVirt Engineにログインし、データセンターツリーのホストタブを選ぶと、アップデートが存在することが表示されます。  
  
InstallationメニューからUpgradeを選びます。  
  
![ovirt-host-upgrade.png](/assets/images/e1c49737-9431-720d-8ecc-343e42268a46.png)  
  
仮想マシンが動いているとアップグレードできません。  
マイグレートするか、シャットダウンします。  
