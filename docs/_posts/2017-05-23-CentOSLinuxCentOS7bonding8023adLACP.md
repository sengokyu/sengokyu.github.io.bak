---
date: '2017-05-23T10:38:40+09:00'
layout: post
published: true
qiita_article_id: 645f947634cc725303dc
tags:
- Linux
- CentOS
title: '[CentOS][Linux] CentOS 7におけるネットワークbonding(802.3ad LACP)の設定'
updated: '2017-05-23T10:38:40+09:00'

---
# はじめに  
  
2つのNICを束ねて、802.3ad LACPを設定します。  
  
## わかっていないこと  
  
複数のNICを束ねて、帯域を稼いだり冗長性を確保したりすることをbondingとかteamingと呼ぶらしいのですが、RedHat/CentOSではbondingとteamingは別口で設定するようになっているみたいです。  
  
bondingをteamingに変換するツールもあったりして、どちらを使えばよいのかわかりません。  
  
ここではbondingを設定します。  
  
# 設定手順  
  
## bondingコネクションを作成  
  
```shell-session
# nmcli connection add save yes type bond autoconnect yes mode 4
```  
  
`nmcli conneection add type bond`に渡すオプションは、`modinfo bond`とすると表示されます。  
  
modeのオプションはこんなのがあるようです。  
  
> mode:Mode of operation; 0 for balance-rr, 1 for active-backup, 2 for balance-xor, 3 for broadcast, 4 for 802.3ad, 5 for balance-tlb, 6 for balance-alb (charp)  
  
こんなんできました。  
  
```shell-session
# nmcli c
NAME    UUID                                  TYPE            DEVICE
bond    cd47091f-ff5a-4a47-882f-970e0afe0fc7  bond            nm-bond
```  
  
名前を指定しなかったので、 *bond* という名前になりました。  
  
  
## 物理NICをbondingコネクションに追加  
  
*ens6f0* と *ens6f1* というNICがあります。  
これらを追加します。  
  
```shell-session
# nmcli connection add save yes type bond-slave ifname ens6f0 master bond
# nmcli connection add save yes type bond-slave ifname ens6f1 master bond
```  
  
こんなんできました。  
  
```shell-session
# nmcli c
NAME               UUID                                  TYPE            DEVICE
bond               cd47091f-ff5a-4a47-882f-970e0afe0fc7  bond            nm-bond
bond-slave-ens6f0  bce1ff10-b17f-42bd-9bb6-40b822761505  802-3-ethernet  ens6f0
bond-slave-ens6f1  883793cd-a03d-46c5-9026-42931dc25ee8  802-3-ethernet  ens6f1
```  
  
## IPアドレスの設定  
  
ここでは *192.168.4.33/24* に設定しました。  
  
```shell-session
# nmcli c modify bond ipv4.method manual ipv4.address 192.168.4.33/24
```  
  
## コネクションを起動  
  
スレーブのほうからコネクションを起動していきます。  
  
```shell-session
# nmcli c up bond-slave-ens6f0
# nmcli c up bond-slave-ens6f1
# nmcli c up bond
```  
  
