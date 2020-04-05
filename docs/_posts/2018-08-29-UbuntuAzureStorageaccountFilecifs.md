---
date: '2018-08-29T10:56:07+09:00'
layout: post
published: true
qiita_article_id: e078992f0369edbb827c
tags:
- Ubuntu
- Azure
title: '[Ubuntu] Azure Storage accountのFile共有をcifs経由でオートマウント'
updated: '2018-08-29T10:56:07+09:00'

---
# これはなに？  
  
Azure File共有(SMB 3.0でアクセスできるやつ)をUbuntuに自動マウントさせました。  
  
# 環境  
  
- Azure Storage account  
- Azure Virtual Machine  
- OS: Ubuntu 18.04  
  
# `autofs`の設定  
  
## インストール  
  
`autofs`をインストールします。  
  
```shell-session
sudo apt-get install autofs
```  
  
## 設定ファイルの作成  
  
`/etc/auto.master`に読み込ませるファイルを作ります。  
ファイル名は適宜。  
  
****  
```shell-session:
sudo mkdir /etc/auto.master.d
sudo vi /etc/auto.master.d/tracker-backup.autofs
```  
  
中身はこんなんです。  
  
**/etc/auto.master.d/tracker-backup.autofs**  
```text:/etc/auto.master.d/tracker-backup.autofs
/-      /etc/auto.tracker-backup
```  
  
マップファイルを作成します。  
  
```shell-session
sudo vi /etc/auto.tracker-backup
```  
  
中身はこんなんです。  
  
**/etc/auto.tracker-backup**  
```text:/etc/auto.tracker-backup
/backup         -fstype=cifs,vers=3.0,username=ユーザ名,password=パスワード,dir_mode=0777,file_mode=0777,sec=ntlmssp              ://ストレージアカウント名.file.core.windows.net/tracker-backup
```  
  
`/-`としてダイレクトマップにしたので、先頭の要素はキーではなく絶対パスを書きます。  
  
パスワードが書いてあるので、ファイルパーミションを変更します。  
  
```
sudo chmod 600 /etc/auto.tracker-backup
```  
  
  
----  
  
:notebook_with_decorative_cover: ノート  
  
長々としたオプションは、Azureポータルから確認できます。  
  
ストレージアカウントを開き、ファイルストレージを選択します。端の…をクリックして[Connect]メニューを選択します。  
  
![image.png](/assets/images/8b41098f-ef00-0b9a-eeba-d684e578bdd2.png)  
  
そうすると、以下のようなものが表示されるので、ここからコピペします。  
  
  
![image.png](/assets/images/c1fe534f-efec-7347-9521-c65442aaaeb3.png)  
  
----  
  
  
## `autofs`を再起動  
  
```shell-session
sudo systemctl reload autofs
```  
  
  
# 確認  
  
`mount`コマンドでマウントされているか確認します。  
  
```shell-session
ls /backup
mount
```  
  
ファイルを作ってみます。  
  
```
touch /backup/hoge
```  
  
Azureポータルで見てみます。  
  
![image.png](/assets/images/a253224f-0f7f-4997-3f0e-d441ffe888bb.png)  
  
できていました。  
