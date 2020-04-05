---
date: '2015-04-15T17:47:02+09:00'
layout: post
published: true
qiita_article_id: b730c56e9a8be8d21a15
tags:
- CentOS
title: 個人的CentOS6セットアップ記録（NFSクライアント、ActiveDirectory連携、PHP、Apache）
updated: '2015-04-15T19:03:32+09:00'

---
社内での開発用にCentOS 6.6を新たにセットアップした際の個人的な記録です。つまりチラ裏ってやつです。  
  
  
このような作業を行いました。  
  
- ネットワークを有効にする  
- IPv6を無効にする  
- yumで国内サーバのみを使う  
- epelリポジトリの追加  
- NFSクライアントの設定  
- DNSサーバへ動的にホスト名を登録  
- Active Directoryへの認証統合  
- PHPとApacheのインストール  
  
# ネットワークを有効にする  
  
インストール直後は無効になっているのがデフォルト？``ONBOOT=no``を``yes``に変更しました。  
  
**/etc/sysconfig/network-scripts/ifcfg-eth0**  
```text:/etc/sysconfig/network-scripts/ifcfg-eth0
ONBOOT=yes
```  
  
ネットワークを有効にします。  
  
****  
```zsh:
/etc/init.d/network restart
```  
  
  
# IPv6を無効にする  
  
たまにAAAAなところがあるので。  
  
**/etc/sysctl.conf**  
```text:/etc/sysctl.conf
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
```  
  
# yumで国内サーバのみを使う  
  
fastestmirrorプラグインを設定し、.jpのサイトのみ使用するようにします。  
  
**/etc/yum/pluginconf.d/fastestmirror.conf**  
```text:/etc/yum/pluginconf.d/fastestmirror.conf
include_only=.jp
```  
  
一応キャッシュをクリアします。  
  
****  
```zsh:
yum clean plugins
```  
  
# epelリポジトリの追加  
  
yumでインストールできます。楽ちん。  
  
****  
```zsh:
yum install epel-release
```  
  
# NFSクライアントの設定  
  
## rpmのインストール  
  
必要なファイルをインストールします。  
マウントにはautofsを使用します。  
  
****  
```zsh:
yum install rpcbind nfs-utils nfs4-acl-tools autofs
```  
  
## autofsの設定  
  
``/etc/auto.net``を使うのが簡単なのですが、NFSサーバ側が``-alldirs``でエクスポートしているのでauto.netが使えません。  
  
``/etc/auto.misc``ファイルに書き足しました。  
  
**/etc/auto.misc**  
```text:/etc/auto.misc
home    -mountvers=4,soft,intr,nodev,tcp,nolock     mynfsserver:/home
```  
  
mountversを付けないと、なぜかPermission deniedになっていました（NFSサーバはFreeeBSD 10）。  
  
## シンボリックリンクの作成  
  
/homeディレクトリへアクセスしたときにautofsが動くようにします。  
  
****  
```zsh:
cd /
rmdir home
ln -s misc/home home
```  
これで、``ls /home``などとすると自動的にマウントされるはずです。  
  
# DNSサーバへ動的にホスト名を登録  
  
dhcp clientからDNSサーバへホスト名を登録するようにします。  
  
**/etc/dhclient-eth0.conf**  
```text:/etc/dhclient-eth0.conf
send fqdn.fqdn "lauren.mydomain.local";
send fqdn.encoded on;
send fqdn.server-update off;
```  
  
# Active Directoryへの認証統合  
  
Active DirectoryをLDAPサーバとして使用することで、ログインID/パスワードを統合します。  
  
## rpmのインストール  
  
****  
```zsh:
yum install nss-pam-ldapd pam_ldap
```  
  
authconfigコマンドで設定する。  
  
## pam_ldapの設定  
  
authconfigだけではだめだったみたいです。  
  
**/etc/pam_ldap.conf**  
```text:/etc/pam_ldap.conf
base dc=mydomain,dc=local

nss_map_objectclass posixAccount user
nss_map_objectclass shadowAccount user
nss_map_attribute uid sAMAccountName
nss_map_attribute homeDirectory unixHomeDirectory
nss_map_attribute shadowLastChange pwdLastSet
nss_map_objectclass posixGroup group
nss_map_attribute uniqueMember member
pam_login_attribute sAMAccountName
pam_filter objectclass=User
pam_password ad

uri ldap://mydomain.local/
ssl no
tls_cacertdir /etc/openldap/cacerts
```  
  
## nss ldapの設定  
  
一応こちらも。  
  
**/etc/nslcd.conf**  
```text:/etc/nslcd.conf
pagesize 1000
referrals off
filter passwd (&(objectClass=user)(!(objectClass=computer))(uidNumber=*)(unixHomeDirectory=*))
map    passwd uid              sAMAccountName
map    passwd homeDirectory    unixHomeDirectory
filter shadow (&(objectClass=user)(!(objectClass=computer))(uidNumber=*)(unixHomeDirectory=*))
map    shadow uid              sAMAccountName
map    shadow shadowLastChange pwdLastSet
filter group  (objectClass=group)
map    group  uniqueMember     member

uid nslcd
gid ldap
uri ldap://mydomain.local/
base dc=mydomain,dc=local
ssl no
tls_cacertdir /etc/openldap/cacerts
```  
  
# PHPとApacheのインストール  
  
## rpmのインストール  
  
****  
```zsh:
yum install php php-devel php-mbstring php-pgsql php-pdo php-xml
```  
  
SQL Serverやxdebugサポートも入れます。  
  
****  
```zsh:
yum --enablerepo=epel php-mssql php-odbc php-pecl-xdebug
```  
  
## ファイアウォールの停止  
  
****  
```zsh:
chkconfig iptables off
chkconfig ip6tables off
service iptables stop
service ip6tables stop
```  
  
## selinuxを無効にする  
  
Apacheから書き込みできるようにするのがしんどかったので、selinuxを無効にしました。  
  
**/etc/selinux/config**  
```text:/etc/selinux/config
SELINUX=disabled
```  
  
# リブート  
  
リブートして期待通りに動いているか確認します。  
