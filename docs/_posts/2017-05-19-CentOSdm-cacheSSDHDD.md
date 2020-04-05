---
date: '2017-05-19T19:26:43+09:00'
layout: post
published: true
qiita_article_id: e85526bb7d578aee295a
tags:
- Linux
- CentOS
- dm-cache
title: '[CentOS] dm-cacheを使用してSSDをHDDのキャッシュにする'
updated: '2017-05-19T19:27:31+09:00'

---
# 環境  
  
- CentOS Linux release 7.3.1611  
- Linux 3.10.0-514.el7.x86_64  
  
2つのドライブがあります。  
  
- /dev/sda1 SSD  
- /dev/sdb1 HDD  
  
SSDをHDDのキャッシュとして設定します。  
  
# 作業手順  
  
lvmcache(7)のマニュアルに従って作業します。  
  
## Volume Groupを作成  
  
Volume Groupを作ります。SSDとHDD両方を所属させないといけないです。  
  
名前は *vg0* にしました。  
  
****  
```shell-session:
# vgcreate vg0 /dev/sda1 /dev/sdb1
```  
  
## 実データ用Logical Volumeを作成  
  
名前は *export* にしました。サイズはHDD全部です。  
  
```shell-session
# lvcreate -n export -l 100%FREE vg0 /dev/sdb1
```  
  
## キャッシュのメタデータ用Logical Volumeを作成  
  
メタデータは、キャッシュデータ自体の1000分の1より小さくしないといけないです。 [^1]  
  
[^1]: 本当はチャンクサイズを決めて、そこから計算するみたい？？ https://www.redhat.com/archives/dm-devel/2012-December/msg00046.html チャンクひとつで16バイト使うそうです。  
  
SSDがあるので、割り算して *457MB* にしました。  
  
名前は *cache0meta* にしました。  
  
```shell-session
# lvcreate -L 457M -n cache0meta vg0 /dev/sda1
```  
  
## キャッシュのデータ用Logical Volumeを作成  
  
SSDの余った領域を使い切ります。  
  
名前は *cache0* にしました。  
  
```shell-session
# lvcreate -l 100%FREE -n cache0 vg0 /dev/sda1
```  
  
## キャッシュプール用Logical Volumeを作成  
  
キャッシュデータと、キャッシュのメタデータをくっつけてキャッシュプールにします。[^2]  
  
```shell-session
# lvconvert --type cache-pool --cachemode writeback --poolmetadata vg0/cache0meta vg0/cache0
```  
  
`--cachemode`でwrite backやwrite throughを選べます。  
  
## キャッシュ用Logical Volumeを作成  
  
  
```shell-session
# lvconvert --type cache --cachepool vg0/cache0 vg0/export
```  
  
# こんなんできました。  
  
```
# lvs -a vg0
  LV              VG  Attr       LSize   Pool     Origin
  [cache0]        vg0 Cwi---C--- 446.19g
  [cache0_cdata]  vg0 Cwi-ao---- 446.19g
  [cache0_cmeta]  vg0 ewi-ao---- 452.00m
  export          vg0 Cwi-a-C---   1.82t [cache0] [export_corig]
  [export_corig]  vg0 owi-aoC---   1.82t
  [lvol0_pmspare] vg0 ewi------- 452.00m
```  
  
  
[^2]: こうしたところ、エクステントが足りないと言われてしまったので、キャッシュデータ用のLVを少し小さく作り直しました。  
  
