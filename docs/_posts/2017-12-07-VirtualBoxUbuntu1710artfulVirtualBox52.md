---
'created_at: ': '2017-12-07T15:05:20+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Ubuntu
      versions: []
    - name: VirtualBox
      versions: []
  state: 0
title: "[VirtualBox] Ubuntu 17.10(artful)\u306B\u6700\u65B0\u7248VirtualBox 5.2\u3092\
  \u30A4\u30F3\u30B9\u30C8\u30FC\u30EB\u3059\u308B"
'updated_at: ': '2017-12-07T16:46:33+09:00'

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
