---
date: '2016-11-08T17:08:42+09:00'
layout: post
published: true
qiita_article_id: 90bee2b005e660510d94
tags:
- CentOS
- KVM
- libvirt
title: '[CentOS 7][KVM] virt-installの典型的（？）な使用例'
updated: '2016-11-08T17:08:42+09:00'

---
# 典型的（？）な使い方  
  
## CentOS 7仮想マシンの作成  
  
こんな条件で作成する場合のコマンド例です。  
  
 - CPU: ホストそのままを使う  
 - メモリ: 1024MB  
 - 画面: 外部からSPICEで接続、コンソールへ自動接続しない(virt-viewerを起動しない)  
 - ネットワーク: ホストのIPアドレスを使ってNAT接続、準仮想化  
 - ISOファイルからインストール  
 - OSの種類はCentOS 7.0  
 - ディスク: ストレージプールから割り当て、容量10GB、準仮想化、ファイル形式はraw  
  
```
virt-install --name guest1\
 --cpu host\
 --memory 1024\
 --graphics spice,listen=0.0.0.0\
 --noautoconsole
 --network network=default,model=virtio
 --location /var/lib/libvirt/images/CentOS-7-x86_64-DVD-1511.iso\
 --os-variant centos7.0\
 --disk pool=default,size=10,bus=virtio,format=raw
```  
  
## Windows仮想ディスクをインポート  
  
 - CPU: Core2Duoをエミュレート(Windows 10だとこれがよいみたい)  
 - メモリ: 1024MB  
 - 画面: 外部からSPICEで接続、コンソールへ自動接続しない(virt-viewerを起動しない)  
 - ネットワーク: macvtapを使ってホストデバイスへ直接続  
 - ディスク: 既存ディスクイメージファイルを使用  
 - OSの種類はWindwos 8.1  
  
```
virt-install --name guest1\
 --cpu core2duo\
 --memory 1024\
 --graphics spice,listen=0.0.0.0\
 --network type=direct,source=ens32\
 --disk path=/var/lib/libvirt/tmp/cpa647ss.raw,bus=sata\
 --import\
 --os-variant win8.1\
 --noautoconsole
```  
  
# 各オプションの説明  
  
## --name NAME  
  
仮想マシン名です。  
  
## --memory 数字  
  
メモリ容量です。単位はMiBです。  
  
## --cpu MODEL[,+feature][,-feature][,match=MATCH][,vendor=VENDOR]  
  
CPUモデルと機能の使用可/不可を指定します。ホストをそのまま見せるときは`host`にします。  
  
## --graphics TYPE,opt1=arg1,opt2=arg2,...  
  
TYPEはvnc、spice、noneを指定できます。  
  
オプションの主なところはこんなんがあります。  
  
|オプション | 説明 |  
|:-----------|:------------|  
|port|ポート番号|  
|listen|リッスンするIPアドレスです。未指定だとlocalhostになります。|  
|password|VNCのパスワードです。|  
  
  
## --os-variant  
  
OSの種類です。  
  
`osinfo-query os`コマンドで使用できるOSが一覧できます。  
  
## --graphics  
  
|オプション | 説明 |  
|:-----------|:------------|  
| listen | |  
  
## --network  
  
|オプション | 説明 |  
|:-----------|:------------|  
| model ||  
  
## --location  
  
## --disk  
  
|オプション | 説明 |  
|:-----------|:------------|  
| model ||  
  
## --noautoconsole  
  
コンソールを開始しません。  
  
  
