---
date: '2017-12-07T15:05:20+09:00'
layout: post
published: true
qiita_article_id: 3e8b0c00eda832689d18
tags:
- Ubuntu
- VirtualBox
title: '[VirtualBox] Ubuntu 17.10(artful)に最新版VirtualBox 5.2をインストールする'
updated: '2017-12-07T16:46:33+09:00'

---
標準レポジトリには5.1.30が入っています。  
最新版の5.2をインストールしてみました。  
  
```shell-session
sudo sh -c "echo 'deb http://download.virtualbox.org/virtualbox/debian '$(lsb_release -cs)' contrib non-free' > /etc/apt/sources.list.d/virtualbox.list"
wget -q http://download.virtualbox.org/virtualbox/debian/oracle_vbox_2016.asc -O- | sudo apt-key add -
sudo apt-get update
sudo apt-get install virtualbox-5.2
```  
  
# インストール結果  
  
5.1とはちょいちょいUI違いますね。  
  
![image.png](/assets/images/afd9e1ff-bb45-fc72-1b83-517ca07faa2d.png)  
  
  
Systemctlのユニットも細かく分かれているみたいです。  
  
```shell-session
% systemctl list-unit-files |grep vbox
vboxautostart-service.service              enabled        
vboxballoonctrl-service.service            enabled        
vboxdrv.service                            enabled        
vboxweb-service.service                    enabled        
```  
  
# トラブル  
  
ゲストOS起動時はNATが動いてネットワークが使えるのに、少し時間が経つと使えなくなります。なんでだろう。。。？  
5.1.30でも同じ症状でした。  
  
## 解決  
  
準仮想化がダメだったようです。Intel MTに変えたら問題解消しました。  
  
# 参考  
  
- https://help.ubuntu.com/community/VirtualBox/Installation  
