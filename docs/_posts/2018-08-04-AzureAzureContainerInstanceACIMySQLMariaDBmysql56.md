---
date: '2018-08-04T18:57:55+09:00'
layout: post
published: true
qiita_article_id: c9979717f9796be0f663
tags:
- MySQL
- Azure
- mariadb
- Docker
title: '[Azure] Azure Container Instance(ACI)でMySQL/MariaDBを使うときはmysql:5.6を使え'
updated: '2018-08-07T19:55:45+09:00'

---
**【追記】**  
  
「Azure Container Instance(ACI)ではMySQL/MariaDBが実質使えない!?かも!?」  
というタイトルで記事を公開しました。  
もう少し試したところ、mysql:5.6イメージであれば動きました。  
  
  
# TL;DR;  
  
* Azure Container Instance(ACI)は、Azure StorageのFile共有をボリュームマウントできる  
* File共有はCIFS(SMBマウント)なので、MySQL/MariaDBからのファイル操作要求に応えられない部分がある  
* 要求に応えられないので、MySQL/MariaDBが動かない  
* MySQL/MariaDBを動かすためには、ボリュームマウントを諦めなくてはいけない  
  
つまり、ACIではMySQL/MariaDBのデータを永続化できない。  
  
  
  
# 試したこと  
  
## Storageアカウントのバージョン  
  
汎用(v1)、汎用(v2)どちらもダメでした。  
冗長はLRSです。  
  
## Dockerイメージ  
  
official mariadb、official mysql、bitnami mariadb、bitnami mysqlのいずれもダメでした。  
  
**【追記】**  
  
official mysql 5.6なら動くかもしれません。  
  
  
  
# （おまけ） ACIの作成  
  
作成するときは、あらかじめyamlファイルを用意してazure cliに食わせます。  
  
ACIは[ポータル](https://portal.azure.com/)からも作成できますが、azure cliからでないとグループを作成できないです。  
  
  
これはmariadbとredmineをデプロイするyamlファイルです。内容的にはdocker composeみたいなものです。  
  
**aci-deploy.yaml**  
```yaml:aci-deploy.yaml
apiVersion: 2018-06-01
location: westus2
name: redmine
properties:
  volumes:
  - name: dbshare
    azurefile:
      shareName: dbshare
      readOnly: false
      storageAccountName: mystore
      storageAccountKey: ***************************************
  - name: redmineshare
    azureFile:
      shareName: redmineshare
      readOnly: false
      storageAccountName: mystore
      storageAccountKey: ***************************************
  containers:
  - name: mariadb
    properties:
      image: mariadb
      resources:
        requests:
          cpu: 1
          memoryInGb: 1
      ports:
      - port: 3306
      environmentVariables:
      - name: MYSQL_ROOT_PASSWORD
        secureValue: password123
      - name: MYSQL_DATABASE
        value: redmine
      volumeMounts:
      - name: dbshare
        mountPath: /var/lib/mysql
  - name: redmine
    properties:
      image: redmine
      resources:
        requests:
          cpu: 1
          memoryInGb: 1
      ports:
      - port: 80
      - port: 443
      environmentVariables:
      - name: REDMINE_DB_MYSQL
        value: localhost
      - name: REDMINE_DB_USERNAME
        secureValue: root
      - name: REDMINE_DB_PASSWORD
        secureValue: password123
      volumeMounts:
      - name: redmineshare
        mountPath: /usr/src/redmine/files
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '443'
    dnsNameLabel: my-redmine
  restartPolicy: Never
tags: null
type: Microsoft.ContainerInstance/containerGroups
```  
  
次のように`az`コマンドを実行します。  
  
```shell-session
az container create -g リソースグループ名 -f aci-deploy.yaml
```  
  
## リファレンス  
  
[yamlファイルのリファレンス](https://docs.microsoft.com/ja-jp/azure/templates/microsoft.containerinstance/containergroups)   
  
  
# ほんとは。。。  
  
「Azure Container InstanceでRedmineを動かす方法」みたいな記事を書くつもりでいたのに、まさか失敗報告になるなんて。。。  
  
