---
date: '2015-04-14T16:53:28+09:00'
layout: post
published: true
qiita_article_id: 75783371e63061f5fe21
tags:
- CentOS
- vmware
title: CentOS 6にVMware Toolsをインストールする。yumで。
updated: '2015-04-14T16:53:28+09:00'

---
付属CD-ROMをマウントしてインストールしてもよいのですが、yumのほうが簡単かも！？  
  
# 環境  
  
- VMware ESXi 5.5  
- CentOS 6.6  
  
# 準備  
  
/etc/yum.repos.d/VMware-Tools.repoというファイルを作成します。  
  
**/etc/yum.repos.d/VMware-Tools.repo**  
```text:/etc/yum.repos.d/VMware-Tools.repo
[vmware-tools]
name=VMware Tools for Red Hat Enterprise Linux $releasever – $basearch
baseurl=http://packages.vmware.com/tools/esx/latest/rhel6/$basearch
enabled=1
gpgcheck=1
gpgkey=http://packages.vmware.com/tools/keys/VMWARE-PACKAGING-GPG-RSA-KEY.pub
```  
  
# yumを実行  
  
Xなしなので、Xなしパッケージをインストールしました。  
  
****  
```text:
# yum install vmware-tools-esx-nox
Loaded plugins: fastestmirror
Setting up Install Process
--> Processing Dependency: vmware-tools-plugins-grabbitmqProxy >= 9.4.11 for package: vmware-tools-esx-nox-9.4.11-1.el6.x86_64
--> Running transaction check
---> Package vmware-tools-core.x86_64 0:9.4.11-1.el6 will be installed
---> Package vmware-tools-foundation.x86_64 0:9.4.11-1.el6 will be installed
---> Package vmware-tools-guestlib.x86_64 0:9.4.11-1.el6 will be installed
---> Package vmware-tools-libraries-nox.x86_64 0:9.4.11-1.el6 will be installed
---> Package vmware-tools-plugins-autoUpgrade.x86_64 0:9.4.11-1.el6 will be installed
---> Package vmware-tools-plugins-deployPkg.x86_64 0:9.4.11-1.el6 will be installed
---> Package vmware-tools-plugins-grabbitmqProxy.x86_64 0:9.4.11-1.el6 will be installed
---> Package vmware-tools-plugins-guestInfo.x86_64 0:9.4.11-1.el6 will be installed
---> Package vmware-tools-plugins-hgfsServer.x86_64 0:9.4.11-1.el6 will be installed
---> Package vmware-tools-plugins-powerOps.x86_64 0:9.4.11-1.el6 will be installed
---> Package vmware-tools-plugins-timeSync.x86_64 0:9.4.11-1.el6 will be installed
---> Package vmware-tools-plugins-vix.x86_64 0:9.4.11-1.el6 will be installed
---> Package vmware-tools-plugins-vmbackup.x86_64 0:9.4.11-1.el6 will be installed
---> Package vmware-tools-services.x86_64 0:9.4.11-1.el6 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

===================================================================================================================================
 Package                                          Arch                Version                      Repository                 Size
===================================================================================================================================
Installing:
 vmware-tools-esx-nox                             x86_64              9.4.11-1.el6                 vmware-tools              3.0 k
Installing for dependencies:
 vmware-tools-core                                x86_64              9.4.11-1.el6                 vmware-tools              3.9 M
 vmware-tools-foundation                          x86_64              9.4.11-1.el6                 vmware-tools              213 k
 vmware-tools-guestlib                            x86_64              9.4.11-1.el6                 vmware-tools               45 k
 vmware-tools-libraries-nox                       x86_64              9.4.11-1.el6                 vmware-tools              2.1 M
 vmware-tools-plugins-autoUpgrade                 x86_64              9.4.11-1.el6                 vmware-tools              5.7 k
 vmware-tools-plugins-deployPkg                   x86_64              9.4.11-1.el6                 vmware-tools               45 k
 vmware-tools-plugins-grabbitmqProxy              x86_64              9.4.11-1.el6                 vmware-tools              338 k
 vmware-tools-plugins-guestInfo                   x86_64              9.4.11-1.el6                 vmware-tools               20 k
 vmware-tools-plugins-hgfsServer                  x86_64              9.4.11-1.el6                 vmware-tools              5.7 k
 vmware-tools-plugins-powerOps                    x86_64              9.4.11-1.el6                 vmware-tools              7.4 k
 vmware-tools-plugins-timeSync                    x86_64              9.4.11-1.el6                 vmware-tools              9.4 k
 vmware-tools-plugins-vix                         x86_64              9.4.11-1.el6                 vmware-tools               68 k
 vmware-tools-plugins-vmbackup                    x86_64              9.4.11-1.el6                 vmware-tools               14 k
 vmware-tools-services                            x86_64              9.4.11-1.el6                 vmware-tools              342 k

Transaction Summary
===================================================================================================================================
Install      15 Package(s)

Total download size: 7.1 M
Installed size: 20 M
```  
  
  
再起動して終了です。お疲れ様でした。  
