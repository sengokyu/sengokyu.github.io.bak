---
date: '2017-12-18T15:03:36+09:00'
layout: post
published: true
qiita_article_id: 6ec991b7b4b4c3495233
tags:
- FreeBSD
- zabbix
- ネットワーク
- 運用
- 監視
title: '[FreeBSD] FreeBSD 11.1にZabbix 3.4をインストールした私的な記録'
updated: '2017-12-19T13:09:45+09:00'

---
Zabbixを動かそうと、久方ぶりにFreeBSD 11.1をインストールしました。  
その記録です。  
  
# OSインストール前に  
  
インストーラを用意します。  
  
1. USBメモリ用のイメージをダウンロードします。  
2. `dd if=イメージファイル of=/dev/USBメモリのデバイス bs=1M conv=sync`としてブータブルUSBメモリを作成します。  
  
# OSインストール  
  
昔と比べて、ウィザード形式っぽい一方通行のインストーラになりました。  
画面に従っていけば終わっちゃいました。  
  
以下は、インストール途中で寄り道したぶんです。  
  
## ルートファイルシステムのミラー化  
  
HDDを2個付けてミラー化しました。  
  
Partitioning画面で、「ZFS(Auto)」を選びます。  
ZFS Configuration画面で次のように設定しました。  
  
| 項目 | 内容 |  
|:--|:--|  
| Pool Type/Disks  | mirrorを選ぶ  |  
| Force 4K Sectors  | 古いHDDなのでNoにしました  |  
| Partition Schema  | GPT  |  
| Swap Size  | 0（あとで作ります)  |  
| Mirro Swap  | No  |  
  
メモリ3GBしかないので、ZFSのプリフェッチは使わないようにするよ、と言われてしまいました。  
  
## 起動するサービス  
  
System Configuration画面で以下のようにしました。  
  
| 項目 | 設定 |  
|:--|:--|  
| local_unbound  |   |  
| sshd  | x  |  
| moused  |   |  
| ntpd  |  |  
| powerd  | x |  
| dumpdev |  |  
  
# OSインストール後  
  
## グループ/ユーザの追加  
  
```shell
pw group add mygroup -g 1000
adduser
```  
  
## プロキシ環境変数の設定  
  
**/etc/csh.cshrc**  
```csh:/etc/csh.cshrc
set proxy = http://proxy.local:8080     
setenv http_proxy $proxy                     
setenv https_proxy $proxy                    
setenv ftp_proxy $proxy                    
```  
  
  
## スワップの追加  
  
```shell
zfs create -V 4G zroot/SWAP
zfs set org.freebsd:swap=on checksum=off compression=off dedup=off sync=disabled primarycache=none zroot/SWAP
```  
  
再起動して確認  
  
```shell-session
# swapctl -s -h -l
Device:            Bytes      Used:
/dev/zvol/zroot/SWAP       4.0G         0B
Total:              4.0G         0B
```  
  
参照: https://wiki.freebsd.org/RootOnZFS#ZFS_Swap_Volume  
  
## アップグレード  
  
```shell
freebsd-update fetch
freebsd-update install
reboot
```  
  
  
## portsの更新  
  
```shell
portsnap fetch
portsnap extract
```  
  
`pkg`使うから、いらなかったかも。。。  
  
  
## パッケージ管理システム  
  
`pkg`コマンドを実行したら、自動的にインストールされました。便利極まりないですね。  
  
```shell-session
# pkg 
The package management tool is not yet installed on your system.
Do you want to fetch and install it now? [y/N]: y
Bootstrapping pkg from pkg+http://pkg.FreeBSD.org/FreeBSD:11:amd64/quarterly, please wait...
Verifying signature with trusted certificate pkg.freebsd.org.2013102301... done
Installing pkg-1.10.1...
Extracting pkg-1.10.1: 100%
pkg: not enough arguments
Usage: pkg [-v] [-d] [-l] [-N] [-j <jail name or id>|-c <chroot path>|-r <rootdir>] [-C <configuration file>] [-R <repo config dir>] [-o var=value] [-4|-6] <command> [<args>]

For more information on available commands and options see 'pkg help'.
```  
  
## sudoをインストール  
  
```shell
pkg install sudo
visudo
```  
  
引き継ぐ環境変数と、sudoを使用できるグループを追記しました。  
  
**sudoers**  
```text:sudoers
Defaults env_keep += "http_proxy https_proxy ftp_proxy"

%wheel ALL=(ALL) ALL
```  
  
## root宛のメールを自分宛てに  
  
```shell
vi /etc/aliases
```  
  
`root:自分のメールアドレス`を追記します。  
  
```shell
newaliases
```  
  
## メールをすべて特定ホストへ転送  
  
```shell
cd /etc/mail
sudo make `hostname`.mc
sudo make `hostname`.mc
```  
  
SMART_HOSTを追記します。  
  
```
sudo vi `hostname`.mc
```  
  
**/etc/mail/ホスト名.mc**  
```mc4:/etc/mail/ホスト名.mc
define(`SMART_HOST', `my.smtp.server')
```  
  
ビルドしてsendmailを再起動します。  
  
```shell
sudo make install
sudo make restart
```  
  
# Zabbixのインストールと設定  
  
やっとこ本題のZabbixです。  
  
## MySQL/Zabbix/Nginxのインストール  
  
**MySQL/Zabbix/Nginxをインストール**  
```shell:MySQL/Zabbix/Nginxをインストール
sudo pkg install mysql56-server
sudo pkg install zabbix34-server
sudo pkg install zabbix34-frontend
sudo pkg install nginx
```  
  
`/etc/rc.conf.local`ファイルに追記します。  
  
**/etc/rc.conf.local**  
```sh:/etc/rc.conf.local
mysql_enable="YES"                           
zabbix_server_enable="YES"                   
php_fpm_enable="YES"                         
nginx_enable="YES"        
```  
  
  
## データベース作成  
  
zabbix34-serverインストール時に、以下のメッセージが表示されました。  
  
```
For Zabbix server and proxy daemons, as well as Zabbix frontend, a database is                                                                                                      
required. It is not needed to run Zabbix agent.                                                                                                                                     
                                                                                                                                                                                    
% cd /usr/local/share/zabbix34/server/database                                                                                                                                      
                                                                                                                                                                                    
MySQL:                                       
shell> mysql -u<username> -p<password>       
mysql> create database zabbix character set utf8;                                         
mysql> quit;                                 
shell> mysql -u<username> -p<password> zabbix < database/mysql/schema.sql                 
shell> mysql -u<username> -p<password> zabbix < database/mysql/images.sql                 
shell> mysql -u<username> -p<password> zabbix < database/mysql/data.sql             
```  
  
指示に従ってデータベースを作成します。  
  
  
**MySQLを起動**  
```shell:MySQLを起動
sudo service mysql-server start
```  
  
**MySQLへ接続**  
```shell:MySQLへ接続
mysql -u root
```  
  
**データベースとユーザを作成**  
```mysql:データベースとユーザを作成
create database zabbix character set utf8;
grant all on zabbix.* to `zabbix_user`@`localhost` identified by 'zabbix_pass';
```  
  
**テーブル等を作成**  
```shell:テーブル等を作成
cd /usr/local/share/zabbix34/server/database/
mysql -u zabbix_user -p zabbix < mysql/schema.sql
mysql -u zabbix_user -p zabbix < mysql/images.sql
mysql -u zabbix_user -p zabbix < mysql/data.sql 
```  
  
## Zabbixサーバの設定と起動  
  
**/usr/local/etc/zabbix34/zabbix_server.conf**  
```ini:/usr/local/etc/zabbix34/zabbix_server.conf
LogFile=/tmp/zabbix_server.log               

DBName=zabbix                                
DBUser=zabbix_user                           
DBPassword=zabbix_pass                       

Timeout=4                                    

LogSlowQueries=3000        
```  
  
  
**zabbixサーバを起動**  
```shell:zabbixサーバを起動
sudo service zabbix_server start
```  
  
## Nginxの設定と起動  
  
Zabbixフロントエンドを動かすためNginxを設定します。  
  
ドキュメントルートを`/usr/local/www`にしました。  
*.phpへのアクセスは、PHP FPMへ回すようにしました。  
  
  
**/usr/local/etc/nginx/nginx.conf**  
```text:/usr/local/etc/nginx/nginx.conf
worker_processes  1;

events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile      on;
    keepalive_timeout 65;
    
    server {
        listen 80;
        root /usr/local/www;
        index index.html index.htm index.php;

        location ~ [^/]\.php(/|$) {
             fastcgi_split_path_info ^(.+?\.php)(/.*)$;

             if (!-f $document_root$fastcgi_script_name) {
                 return 404;
             }

             fastcgi_index index.php;
             fastcgi_pass 127.0.0.1:9000;
             fastcgi_param HTTP_PROXY "";
             fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

             # include the fastcgi_param setting
             include fastcgi_params;
        }
    }
}
```  
  
  
**php-fpmとnginxを起動**  
```shell:php-fpmとnginxを起動
sudo service php-fpm start
sudo service nginx start
```  
  
ブラウザからhttp://ホスト名/zabbix34/へアクセスすると、こんな画面が表示されます。  
  
![image.png](/assets/images/35ba52b2-ce5e-4e19-0bc7-29d46da9fc21.png)  
  
  
## Zabbixフロントエンドの設定  
  
設定を進めると、こんなメッセージが表示されました。  
  
-    Minimum required size of PHP post is 16M (configuration option "post_max_size").  
-    Minimum required limit on execution time of PHP scripts is 300 (configuration option "max_execution_time").  
-    Time zone for PHP is not set (configuration parameter "date.timezone").  
-    PHP option "always_populate_raw_post_data" must be set to "-1"  
  
指示に従い設定します。  
  
**/usr/local/etc/php/zabbix-required.ini**  
```ini:/usr/local/etc/php/zabbix-required.ini
post_max_size = 16M
max_execution_time = -1
always_populate_raw_post_data = -1

[Date]
date.timezone = Asia/Tokyo
```  
  
**php-fpmを再起動**  
```shell:php-fpmを再起動
sudo service php-fpm restart
```  
All OKになりました。  
  
![image.png](/assets/images/47fcbc1f-d823-a54a-8005-69055d6bd10d.png)  
  
データベース接続先を設定します。  
  
![image.png](/assets/images/0209a4f7-7940-988e-25e0-bfef4c4d0852.png)  
  
Zabbixサーバを指定します。  
  
![image.png](/assets/images/08be5042-9ca7-6654-947f-e9b316e71e5e.png)  
  
確認。  
  
![image.png](/assets/images/4aa743e9-e932-d4d9-0bb2-69da6cb90252.png)  
  
設定ファイル書き込めないから、自前でダウンロードして置きなさいと言われてしまいました。  
  
![image.png](/assets/images/eee0b567-c987-e3d5-ba55-2396a9869905.png)  
  
ファイルを自前で置いて、[Finish]をクリック。  
終わりました〜〜 :smile:   
  
![image.png](/assets/images/09904aa1-ade2-042b-7c50-71ad6d93a9bd.png)  
  
# おしまい  
  
初期パスワードはadmin/zabbixです。  
  
![image.png](/assets/images/5971e1f9-38a0-becd-6b55-f24a1a21415a.png)  
  
ログインしたら、せっせと監視対象を追加していきましょう。  
  
![image.png](/assets/images/233d1cd5-c859-e78b-6c07-4d0d0f3fc9d2.png)  
  
