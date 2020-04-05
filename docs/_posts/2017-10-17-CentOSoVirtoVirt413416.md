---
date: '2017-10-17T13:26:02+09:00'
layout: post
published: true
qiita_article_id: dacabb302e93f8d4cc78
tags:
- CentOS
- oVirt
title: '[CentOS][oVirt] oVirt 4.1.3から4.1.6へアップグレード'
updated: '2017-10-17T13:26:02+09:00'

---
CentOS 7で動いているoVirtを4.1.3から4.1.6へアップグレードしました。  
  
内容的には[前回](2017-07-14-CentOSoVirtoVirt411413.md)と一緒かな。  
  
下記手順は、[リリースノート](https://www.ovirt.org/release/4.1.6/#install--upgrade-from-previous-versions)に書いてある通りです。  
  
# 手順1 yumレポジトリをアップグレード  
  
```shell-session
# yum install http://resources.ovirt.org/pub/yum-repo/ovirt-release41.rpm
```  
  
# 手順2 パッケージをインストール  
  
```shell-session
# yum update "ovirt-*-setup*"
```  
  
# 手順3 `engin-setup`を実行  
  
```shell-session
# engine-setup
```  
  
`engine-setup`を実行すると、いくつか質問が表示されます。たぶんにEnterキーを押していくだけで大丈夫です。自分はサーバ証明書だけ更新しました。  
  
  
* パッケージの更新はYes  
  
****  
```text:
          Setup has found updates for some packages:
          PACKAGE: [updated] ovirt-engine-4.1.3.5-1.el7.centos.noarch
          PACKAGE: [update] ovirt-engine-4.1.6.2-1.el7.centos.noarch
          PACKAGE: [updated] ovirt-engine-backend-4.1.3.5-1.el7.centos.noarch
          PACKAGE: [update] ovirt-engine-backend-4.1.6.2-1.el7.centos.noarch
          PACKAGE: [updated] ovirt-engine-dbscripts-4.1.3.5-1.el7.centos.noarch
          PACKAGE: [update] ovirt-engine-dbscripts-4.1.6.2-1.el7.centos.noarch
          PACKAGE: [updated] ovirt-engine-dwh-4.1.1-1.el7.centos.noarch
          PACKAGE: [update] ovirt-engine-dwh-4.1.7-1.el7.centos.noarch
          PACKAGE: [updated] ovirt-engine-extension-aaa-jdbc-1.1.5-1.el7.centos.noarch
          PACKAGE: [update] ovirt-engine-extension-aaa-jdbc-1.1.6-1.el7.centos.noarch
          PACKAGE: [updated] ovirt-engine-extensions-api-impl-4.1.3.5-1.el7.centos.noarch
          PACKAGE: [update] ovirt-engine-extensions-api-impl-4.1.6.2-1.el7.centos.noarch
          PACKAGE: [updated] ovirt-engine-restapi-4.1.3.5-1.el7.centos.noarch
          PACKAGE: [update] ovirt-engine-restapi-4.1.6.2-1.el7.centos.noarch
          PACKAGE: [updated] ovirt-engine-tools-4.1.3.5-1.el7.centos.noarch
          PACKAGE: [update] ovirt-engine-tools-4.1.6.2-1.el7.centos.noarch
          PACKAGE: [updated] ovirt-engine-tools-backup-4.1.3.5-1.el7.centos.noarch
          PACKAGE: [update] ovirt-engine-tools-backup-4.1.6.2-1.el7.centos.noarch
          PACKAGE: [updated] ovirt-engine-userportal-4.1.3.5-1.el7.centos.noarch
          PACKAGE: [update] ovirt-engine-userportal-4.1.6.2-1.el7.centos.noarch
          PACKAGE: [updated] ovirt-engine-vmconsole-proxy-helper-4.1.3.5-1.el7.centos.noarch
          PACKAGE: [update] ovirt-engine-vmconsole-proxy-helper-4.1.6.2-1.el7.centos.noarch
          PACKAGE: [updated] ovirt-engine-webadmin-portal-4.1.3.5-1.el7.centos.noarch
          PACKAGE: [update] ovirt-engine-webadmin-portal-4.1.6.2-1.el7.centos.noarch
          PACKAGE: [updated] ovirt-engine-websocket-proxy-4.1.3.5-1.el7.centos.noarch
          PACKAGE: [update] ovirt-engine-websocket-proxy-4.1.6.2-1.el7.centos.noarch
          do you wish to update them now? (Yes, No) [Yes]:
```  
  
* firewalldの設定はYes  
  
```
          --== NETWORK CONFIGURATION ==--

          Setup can automatically configure the firewall on this system.
          Note: automatic configuration of the firewall may overwrite current settings.
          Do you want Setup to configure the firewall? (Yes, No) [Yes]:
```  
  
* データベースのバックアップはYes  
  
```
          --== DATABASE CONFIGURATION ==--

          The detected DWH database size is 241 MB.
          Setup can backup the existing database. The time and space required for the database backup depend on its size. This process takes time, and in some cases (for instance, when the size is few GBs) may take several hours to complete.
          If you choose to not back up the database, and Setup later fails for some reason, it will not be able to restore the database and all DWH data will be lost.
          Would you like to backup the existing database before upgrading it? (Yes, No) [Yes]:
```  
  
* PostgreSQLのvacuum実行はNo  
  
```
          --== OVIRT ENGINE CONFIGURATION ==--

          Perform full vacuum on the engine database engine@localhost?
          This operation may take a while depending on this setup health and the
          configuration of the db vacuum process.
          See https://www.postgresql.org/docs/9.0/static/sql-vacuum.html
          (Yes, No) [No]:
```  
  
* サーバ証明書の更新はYes  
  
```
          --== PKI CONFIGURATION ==--

          One or more of the certificates should be renewed, because they expire soon, or include an invalid expiry date, or do not include the subjectAltName extension, which can cause them to be rejected by recent browsers.
          If you choose "No", you will be asked again the next time you run Setup.
          See https://www.ovirt.org/develop/release-management/features/infra/pki-renew/ for more details.
          Renew certificates? (Yes, No) [No]: Yes
```  
  
# 手順4 ホストのアップグレード  
  
管理画面からホストを右クリックし、、Installation > Upgradeメニューを選択します。  
