---
date: '2019-03-13T20:45:13+09:00'
layout: post
published: true
qiita_article_id: 80fe0118a81e65b9a9a7
tags:
- CentOS
- KVM
- クラウド
- oVirt
title: '[oVirt] 仮想マシンマネージャ(oVirt Engine)の故障HDDを交換したが、驚くほど簡単過ぎた'
updated: '2019-03-21T23:15:11+09:00'

---
# 作業内容サマリー  
  
作業する内容のサマリーです。  
  
1. ハードディスクを交換  
2. CentOS 7を新規インストール  
3. CentOSをアップデート  
4. oVirt yumレポジトリを追加  
5. oVirtをインストール  
6. バックアップファイルからレストア  
7. oVirtをセットアップ  
  
  
# 作業内容詳細  
  
## ステップ 1. 〜 3.   
  
ステップ 1. 〜 3. は、特別なことはないので省略します。  
  
  
## ステップ 4. oVirt yumレポジトリを追加  
  
`yum install http://resources.ovirt.org/pub/yum-repo/ovirt-release42.rpm` [^1] を実行します。  
[^1]: 2019年3月現在の最新版は4.3.1ですが、バックアップは4.2.6なので、4.2をインストールしました。  
  
続いて `yum update` を実行します。  
  
  
## ステップ 5. oVirtをインストール  
  
`yum install ovirt-engine` を実行します。  
  
  
## ステップ 6. バックアップファイルからレストア  
  
`engine-backup`コマンドでレストアもできます。  
データベースも同一ホストで動いているので、すべてをレストアしました。  
  
実行するコマンドはこのようになります。  
`engine-backup --mode=restore --file=バックアップファイルへのパス--log=engine-restore.log --provision-db --provision-dwh-db --restore-permissions`  
  
  
### `engine-backup`コマンドのオプション  
  
コマンドのオプションを説明します。  
  
`--mode=restore`  
レストアを実行します。  
  
`--file=ファイル名`  
バックアップファイル名を指定します。  
  
`--log=ファイル名`  
ログを記録するファイル名を指定します。  
  
`--provision-db`  
データベースを作成させます。  
  
`--provision-dwh-db`  
データウェアハウス用データベースを作成させます。  
  
`--restore-permissions`  
データベースを作成するときに、アクセス権も復旧させます。  
  
  
### 実行結果  
  
実行すると、このようなメッセージが表示されます。  
  
```
Preparing to restore:
- Unpacking file '/backup02/ovirt/engine-backup-20190313.bak'
Restoring:
- Files
Provisioning PostgreSQL users/databases:
- user 'engine', database 'engine'
- user 'ovirt_engine_history', database 'ovirt_engine_history'
Restoring:
- Engine database 'engine'
  - Cleaning up temporary tables in engine database 'engine'
  - Updating DbJustRestored VdcOption in engine database
  - Resetting DwhCurrentlyRunning in dwh_history_timekeeping in engine database
  - Resetting HA VM status
------------------------------------------------------------------------------
Please note:

The engine database was backed up at 2019-03-13 18:38:08.000000000 +0900 .

Objects that were added, removed or changed after this date, such as virtual
machines, disks, etc., are missing in the engine, and will probably require
recovery or recreation.
------------------------------------------------------------------------------
- DWH database 'ovirt_engine_history'
You should now run engine-setup.
Done.
```  
  
## ステップ 7. oVirtをセットアップ  
  
`engine-setup`を実行します。  
  
質問にはすべてデフォルトで回答して大丈夫でした。  
  
  
# 感想  
  
ここまで簡単＆親切なのはすごすぎる！  
oVirt最高！！  
  
# 落ち穂拾い  
  
自分のところでは、ユーザー認証のためにLDAP(ActiveDirectory)を使っていたのでした。  
  
https://www.ovirt.org/documentation/admin-guide/chap-Users_and_Roles.html にある手順に従って設定しました。  
  
  
