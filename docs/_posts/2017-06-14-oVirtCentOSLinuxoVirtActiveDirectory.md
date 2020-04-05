---
date: '2017-06-14T15:01:55+09:00'
layout: post
published: true
qiita_article_id: 2b75552d39b2540832e3
tags:
- Linux
- CentOS
- Cloud
- oVirt
title: '[oVirt][CentOS][Linux] oVirtのユーザ認証をActiveDirectoryに任せる'
updated: '2017-06-14T15:01:55+09:00'

---
Active DirectoryのユーザIDとパスワードでoVirtにログインできるようにします。  
  
こちらに詳しい手順が書かれているのでなぞります。  
http://www.ovirt.org/documentation/admin-guide/chap-Users_and_Roles/  
  
  
# 必要なものをインストール  
  
ovirt-engine-extension-aaa-ldap-setupをインストールします。  
  
****  
```shell-session:
# yum install ovirt-engine-extension-aaa-ldap-setup
```  
  
****  
```shell-session:
====================================================================================================================================================================================
 Package                                                       Arch                           Version                                       Repository                         Size
====================================================================================================================================================================================
Installing:
 ovirt-engine-extension-aaa-ldap-setup                         noarch                         1.3.1-1.el7.centos                            ovirt-4.1                          37 k
Installing for dependencies:
 ovirt-engine-extension-aaa-ldap                               noarch                         1.3.1-1.el7.centos                            ovirt-4.1                         109 k
 python-ldap                                                   x86_64                         2.4.15-2.el7                                  base                              159 k
 unboundid-ldapsdk                                             noarch                         3.2.0-1.el7                                   ovirt-4.1                         1.9 M

Transaction Summary
====================================================================================================================================================================================
Install  1 Package (+3 Dependent packages)
```  
  
# ovirt-engine-extension-aaa-ldap-setupを実行  
  
コマンドを実行します。質問が表示されますので、それに答えていきます。  
  
****  
```shell-session:
# ovirt-engine-extension-aaa-ldap-setup
```  
  
LDAP環境はActive Directoryを選びます。  
  
```                                                                                                                         
          Available LDAP implementations:                                                                                                                                           
           1 - 389ds                                                                                                                                                                
           2 - 389ds RFC-2307 Schema                                                                                                                                                
           3 - Active Directory                                                                                                                                                     
           4 - IBM Security Directory Server                                                                                                                                        
           5 - IBM Security Directory Server RFC-2307 Schema                                                                                                                        
           6 - IPA                                                                                                                                                                  
           7 - Novell eDirectory RFC-2307 Schema                                                                                                                                    
           8 - OpenLDAP RFC-2307 Schema                                                                                                                                             
           9 - OpenLDAP Standard Schema                                                                                                                                             
          10 - Oracle Unified Directory RFC-2307 Schema                                                                                                                             
          11 - RFC-2307 Schema (Generic)                                                                                                                                            
          12 - RHDS                                                                                                                                                                 
          13 - RHDS RFC-2307 Schema                                                                                                                                                 
          14 - iPlanet                                                                                                                                                              
          Please select: 3                        
```  
  
ドメイン名を入力します。  
  
```
Please enter Active Directory Forest name: my.example.com
```  
  
LDAP接続するときのプロトコルを選びます。TLSかSSLにしたいところですが、CAの証明書を入手してもうまくいかないので、plainを選びました。  
  
ちなみにCAの証明書を取得する方法は[こちら](https://docs.oracle.com/cd/E19693-01/819-0993/6n3co69n7/index.html) [^1]  
  
[^1]: `ldapsearch -h CR-hostname -D administrator_DN -w administrator_password -b "cn=configuration,dc=my,dc=example,dc=com" "cacertificate=*"` の出力結果から先頭のものを使います。  
  
  
```
Please select protocol to use (startTLS, ldaps, plain) [startTLS]: plain    
```  
  
LDAP検索時に使用するユーザのDNを入力します。  
  
```
Enter search user DN (for example uid=username,dc=example,dc=com or leave empty for anonymous): cn=administrator,cn=users,dc=my,dc=example,dc=com
```  
  
先に入力したユーザのパスワードを入力します。  
  
```
Enter search user password:
```  
  
プロファイル名を入力します。ここで入力した名前がoVirtの初期画面に表示されます。  
  
```
Please specify profile name that will be visible to users [my.example.com]:
```  
  
具体的には、ここの「Profile」の選択肢です。  
  
![ovirt-login.png](/assets/images/2faf4e7d-977a-c875-99ed-e588f674c02d.png)  
  
最後に動作確認ができるようになっています。「Login」や「Search」を選び、適当にユーザ名を入力してみてください。  
  
確認できたら、「Done」と入力するとセットアップが終わります。  
  
以下のように、ファイルが３つ作成されるようです。  
  
```
The following files were created:                                                                                                                                         
    /etc/ovirt-engine/aaa/my.example.com.properties                                                                                                                           
    /etc/ovirt-engine/extensions.d/my.example.com-authz.properties                                                                                                            
    /etc/ovirt-engine/extensions.d/my.example.com-authn.properties               
```  
  
# oVirt engineを再起動  
  
設定を反映させるため、oVirt Engineを再起動します。  
  
```shell-session
# systemctl restart ovirt-engine
```  
  
# ユーザにロールを割り当て  
  
oVirtにログインし、右上の[Configure]をクリックします。  
  
![ovirt-configure.png](/assets/images/6b1984d3-70a0-451f-a9b5-f0ce413d1987.png)  
  
[System Permissions]を選択し、[Add]をクリックします。  
  
![ovirt-configure2.png](/assets/images/4f374d4f-0ef7-009f-f264-7a411745ad4f.png)  
  
プロファイルを選択し、[GO]をクリックします。  
  
![ovirt-configure3.png](/assets/images/bb370ee2-75f4-51ac-8e8e-fc3a85e28953.png)  
  
ユーザ一覧が表示されますので、使用できるユーザを選んでください。  
  
