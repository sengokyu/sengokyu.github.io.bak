---
date: '2016-01-08T12:21:00+09:00'
layout: post
published: true
qiita_article_id: db7cd02584f1835099d6
tags:
- Linux
- CentOS
- centos7
title: 【CentOS 7】CentOS 7をデスクトップとして使うための私的初期設定まとめ
updated: '2016-07-20T09:33:08+09:00'

---
Windowsが息苦しくなってきたのでCentOS 7をデスクトップとして使うことにしました。  
  
# ホスト名を動的にネームサーバへ登録  
  
ActiveDirectoroyのDNSへホスト名を登録するようにします。  
  
NetoworkManagerが動的に生成する`dhclient.conf`ファイル(`/var/lib/NetworkManager/dhclient-インターフェース名.conf`）に足りないものを補います。  
  
**/etc/dhcp/dhclient.conf**  
```text:/etc/dhcp/dhclient.conf
send fqdn.encoded on;
send fqdn.server-update on;
```  
  
# ホームディレクトリをオートマウント  
  
NFSサーバにあるホームディレクトリを自動でマウントするようにします。  
  
nfs-utilsとautofsをインストールします。  
  
```
# yum install nfs-utils autofs
```  
  
autofsを起動します。  
  
```
# systemctl start autofs
```  
  
わかりやすくするためにいったんマウントさせます。  
  
```
# ls /net/NFSサーバ名
```  
  
`/home`を消して、`/net`以下へシンボリックリンクします。  
  
```
# cd /
# rmdir home
# ln -s /net/NFSサーバ名/マウント/ポイント home
```  
  
# ユーザ認証をActiveDirectoroyに任せる  
  
いくつかやり方があるみたいです。  
  
- realmd  
- sssd  
- pam_ldap  
  
上のほうが新しく、下のほうが古いです。  
  
参考: [Ways to Integrate Active Directory and Linux Environments](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/introduction.html)  
  
ADに参加させるのも面倒なので、pam_ldapでいきます。  
  
## pam_ldapをインストール  
  
```
# yum install nss-pam-ldapd
```  
  
## 設定  
  
`authconfig`コマンドで設定します。  
  
```
# authconfig --enableldap --update
# authconfig --enableldapauth --update
# authconfig --ldapserver=LDAPサーバ --update
# authconfig --ldapbasedn=検索対象が入っているDN --update
```  
  
オブジェクトと属性のマッピング等を追記します。  
  
**/etc/nslcd.conf**  
```text:/etc/nslcd.conf
pagesize 1000
referrals off
idle_timelimit 800
filter passwd (&(objectClass=user)(!(objectClass=computer))(uidNumber=*)(unixHomeDirectory=*))
map    passwd uid              sAMAccountName
map    passwd homeDirectory    unixHomeDirectory
map    passwd gecos            displayName
filter shadow (&(objectClass=user)(!(objectClass=computer))(uidNumber=*)(unixHomeDirectory=*))
map    shadow uid              sAMAccountName
map    shadow shadowLastChange pwdLastSet
filter group  (objectClass=group)
```  
  
nscdのnslcdを起動するようして、起動します。  
  
```
# systemctl enable nscd
# systemctl enable nslcd
# systemctl restart nscd
# systemctl restart nslcd
```  
  
## 確認  
  
`id ユーザ名`として、正しい結果が返るか確認します。  
  
## トラブルシュート  
  
tcpdumpでldapを観察するのが早いと思われます。  
設定に従ったリクエストが飛んでいるか、結果が返ってきているかを見ます。  
  
```
# tcpdump -n -i インターフェース -s 0 -w ldap.cap tcp and port 389
```  
  
できたファイルはWiresharkで開きます。  
  
# ホームディレクトリの名前を英語に  
  
デフォルトだとこんな感じで嫌なので、英語にします。  
  
```
ダウンロード  テンプレート  デスクトップ  ドキュメント  ビデオ      音楽      画像      公開
```  
  
****  
```zsh:
$ LANG=C xdg-user-dirs-gtk-update
Gtk-Message: GtkDialog mapped without a transient parent. This is discouraged.
Moving DESKTOP directory from デスクトップ to Desktop
Moving DOWNLOAD directory from ダウンロード to Downloads
Moving TEMPLATES directory from テンプレート to Templates
Moving PUBLICSHARE directory from 公開 to Public
Moving DOCUMENTS directory from ドキュメント to Documents
Moving MUSIC directory from 音楽 to Music
Moving PICTURES directory from 画像 to Pictures
Moving VIDEOS directory from ビデオ to Videos
```  
  
\# WindowsがVistaから実フォルダ名を英語にして、見かけだけを日本語にしたのと逆行しているのはおもしろい。  
  
# プロキシの設定  
  
アプリケーション > システムツール > 設定 > ネットワークの中にプロキシを設定するところがあります。  
  
## sudoしたときにもプロキシを使うようにする  
  
visudoしてsudoの設定に追記  
  
```
Defaults    env_keep += "http_proxy https_proxy ftp_proxy all_proxy no_proxy"
```  
  
# yum  
  
## yumのダウンロード元を.jpドメインに制限  
  
/etc/yum/pluginconf.d/fastestmirror.conf ファイルに追記  
  
```
include_only=.jp
```  
  
## yumのプロキシを設定  
  
yum.confを書き換えず、sudoしたとき用の設定でOKでした。  
  
  
# CapslockをCtrlキーにする  
  
どこで設定しようか迷いましたが、xkb-optionsにしました。  
  
****  
```zsh:
$ gsettings set org.gnome.desktop.input-sources xkb-options "['ctrl:nocaps']"
```  
  
# シェルをzshにする  
  
zshをインストール  
  
****  
```zsh:
$ sudo yum install zsh
```  
  
chshコマンドで変更  
  
****  
```zsh:
$ chsh
Changing shell for sengokyu.
New shell [/bin/bash]: /bin/zsh
Password: 
Shell changed.
```  
  
# 見た目をいろいろ  
  
Tweak Toolを使って設定しました。  
  
タイトルバーが大きすぎるので、まずはそれから。  
  
- Apperance > Theme AdwaitaをEscoに  
- Fonts > Window Titles フォントサイズを11から9に  
- Fonts > Interface フォントサイズを11から9に  
- Fonts > Angialiasing GrayscaleからRgbaに  
- Top Bar > Show date をチェック  
  
\# CapslockをCtrlにする設定もここにあった！  
  
# IMEの切り替えを[Win]+[Space]から[Alt]+[Above_Tab]へ変更する  
  
switch-groupの設定とかぶるので、そちらも変更します。  
  
```
% gsettings set org.gnome.desktop.wm.keybindings switch-input-source "['<Alt>Above_Tab']"

% gsettings set org.gnome.desktop.wm.keybindings switch-group "['<Super>Above_Tab']"
```  
  
\# [Win]キー小さいキーボードなのでつらいっす。  
  
# epelレポジトリの追加  
  
```
$ sudo yum install epel-release
```  
  
# アプリいろいろ  
  
yumでインストールしました。  
  
- tmux  
- filezilla  
- virt-manager  
- dconf-editor  
- lv  
  
## Google Chrome  
  
/etc/yum.repos.d/google-chrome.repoファイルを作成  
  
```
[google-chrome]
name=google-chrome
baseurl=http://dl.google.com/linux/chrome/rpm/stable/x86_64
enabled=1
gpgcheck=1
gpgkey=https://dl.google.com/linux/linux_signing_key.pub
```  
  
****  
```zsh:
$ sudo yum install google-chrome-stable
```  
  
## Oracle jdk8  
  
http://www.oracle.com/technetwork/java/javase/downloads/index.html からRPMをダウンロード  
  
RPMをインストール  
  
****  
```zsh:
% sudo rpm -Uvh jdk-8u66-linux-x64.rpm                   
```  
  
alternativesコマンドでOracle Javaを選択  
  
****  
```zsh:
% sudo alternatives --config java

There are 3 programs which provide 'java'.

  Selection    Command
-----------------------------------------------
   1           /usr/lib/jvm/java-1.7.0-openjdk-1.7.0.91-2.6.2.3.el7.x86_64/jre/bin/java
*+ 2           /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.65-3.b17.el7.x86_64/jre/bin/java
   3           /usr/java/jdk1.8.0_66/jre/bin/java

Enter to keep the current selection[+], or type selection number: 3
```  
  
バージョンを確認。JavaがOpenJDKになっていなければOK  
  
****  
```zsh:
% java -version
java version "1.8.0_66"
Java(TM) SE Runtime Environment (build 1.8.0_66-b17)
Java HotSpot(TM) 64-Bit Server VM (build 25.66-b17, mixed mode)
```  
  
## NetBeans  
  
https://netbeans.org/downloads/ からダウンロード  
  
インストーラを実行してインストール  
  
****  
```zsh:
% sudo sh ./netbeans-8.1-javaee-linux.sh
```  
  
インストールの最後にNetBeansが起動しますが、それはそのまま修了します。  
  
## Windowsゲスト用のドライバ  
  
KVMで動かすWindowsゲスト用のドライバです。  
  
yumのリポジトリを追加してインストールします。  
  
****  
```zsh:
% sudo wget https://fedorapeople.org/groups/virt/virtio-win/virtio-win.repo -O /etc/yum.repos.d/virtio-win.repo
% sudo yum install virtio-win
```  
  
## unar  
  
ファイル名の文字コード判別機能つきの解凍ツールです。  
epel-testingにありました。  
  
****  
```zsh:
% sudo yum --enablerepo=epel-testing install unar
（略）

Dependencies Resolved

=====================================================================================================================================
 Package                             Arch                     Version                           Repository                      Size
=====================================================================================================================================
Installing:
 unar                                x86_64                   1.8.1-10.el7                      epel-testing                   1.9 M
Installing for dependencies:
 gnustep-base-libs                   x86_64                   1.24.7-1.el7                      epel                           1.7 M
 libobjc                             x86_64                   4.8.5-4.el7                       base                            72 k

Transaction Summary
=====================================================================================================================================
Install  1 Package (+2 Dependent packages)

Total download size: 3.7 M
```  
  
## MySQL Workbench  
  
http://dev.mysql.com/downloads/repo/yum/ からYumレポジトリ定義をダウンロードしてインストールしてから、yumでインストールしました。  
  
****  
```zsh:
% sudo yum install mysql-workbench-community      
（略）
Dependencies Resolved

=====================================================================================================================================
 Package                                Arch                Version                         Repository                          Size
=====================================================================================================================================
Installing:
 mysql-workbench-community              x86_64              6.3.6-1.el7                     mysql-tools-community               18 M
Installing for dependencies:
 libzip                                 x86_64              0.10.1-8.el7                    base                                48 k
 postgresql-libs                        x86_64              9.2.14-1.el7_1                  updates                            231 k
 proj                                   x86_64              4.8.0-4.el7                     epel                               181 k
 python-crypto                          x86_64              2.6.1-1.el7.centos              extras                             470 k
 python-ecdsa                           noarch              0.11-3.el7.centos               extras                              69 k
 python-paramiko                        noarch              1.15.1-1.el7                    epel                               999 k
 tinyxml                                x86_64              2.6.2-3.el7                     epel                                49 k
 unixODBC                               x86_64              2.3.1-11.el7                    base                               413 k

Transaction Summary
=====================================================================================================================================
Install  1 Package (+8 Dependent packages)

Total download size: 20 M
Installed size: 125 M
```  
  
