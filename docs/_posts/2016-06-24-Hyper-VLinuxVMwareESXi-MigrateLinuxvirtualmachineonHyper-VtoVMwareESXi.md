---
date: '2016-06-24T11:27:36+09:00'
layout: post
published: true
qiita_article_id: b19a066132478d3806c3
tags:
- Linux
- CentOS
- vmware
- Hyper-V
title: Hyper-V上のLinux仮想マシンをVMware ESXiへ移行する手順 - Migrate Linux virtual machine on Hyper-V
  to VMware ESXi
updated: '2016-06-29T10:35:41+09:00'

---
# 概要  
  
Hyper-V上で動いているLinux仮想マシン（OSはCentOS 5.8）をVMware ESXi 5へ移行しました。  
  
移行するやり方は2通りあります。  
  
- VMware vCenter Converter https://my.vmware.com/web/vmware/evalcenter?p=converter を使った簡単なやり方  
- 仮想ディスクを変換して、ごにょごにょする面倒なやり方  
  
今回は後者でやりました。  
  
# 手順  
  
## ステップ1 仮想ディスクvhdxをvmdkへ変換する  
  
Hyper-Vサーバ上にはある仮想ディスクを変換します。  
  
変換にはStarWindのV2V Converterを使いました。  
  
下記からダウンロードできます。  
https://www.starwindsoftware.com/converter  
  
変換先としてVMware pre-allocated imageを選択しました。  
  
![v2v1.PNG](/assets/images/efa9569d-b05f-8b1f-6e21-9803ed98b461.png)  
  
ほんとうはthickディスクじゃなくてthinディスクにしたいのですが、なぜかESXiで読み込めませんでした。後でもう一度thinに変換することにします。  
  
## ステップ2 VMware仮想マシンを作成  
  
VMware ESXi上で仮想マシンを作成します。  
このとき仮想ディスクは作らないようにします。  
  
## ステップ3 変換した仮想ディスクvmdkをアップロード  
  
変換した仮想ディスクをVMware ESXiからアクセスできる場所へアップロードします。  
  
## ステップ4 仮想マシンへ仮想ディスクを追加  
  
ステップ2で作成した仮想マシンに、ステップ3でアップロードした仮想ディスクを追加します。  
  
SCSIコントローラは、CentOS 5なので LSI Logic SASを選びました。  
より新しいOSであれば、VMware paravirtualのほうがよいです。  
  
## ステップ5 仮想マシンをLive DVDを使って起動  
  
rootファイルシステムが見えなくて起動しないので、いったんLive DVDで起動します。  
  
自分はCentOS 7のDVDを使いました。  
  
## ステップ6 /etc/modprobe.confを書き換えて、initrdを再作成  
  
既存CentOSが`/mnt/sysimage`にマウントされているはずなので、その中にある/etc/modprobe.confを書き換えます。  
  
```shell-session
vi /mnt/sysimage/etc/modprobe.conf
```  
  
書き換え内容は、`scsi_controller`を含む行です。  
  
**/mnt/sysimage/etc/modprobe.conf**  
```text:/mnt/sysimage/etc/modprobe.conf
alias scsi_hostadapter mptbase
alias scsi_hostadapter1 mptscsih
alias scsi_hostadapter2 mptfc
alias scsi_hostadapter3 mptspi
alias scsi_hostadapter4 mptsas
```  
  
[CentOS wiki](https://wiki.centos.org/TipsAndTricks/CreateNewInitrd)にある手順に従って、initrdを再作成していきます。  
  
必要なものをマウントして、chrootします。  
  
```shell-session
mount --bind /proc /mnt/sysimage/proc
mount --bind /dev /mnt/sysimage/dev
mount --bind /sys /mnt/sysimage/sys
chroot /mnt/sysimage
```  
  
initrdを作成します。wikiにある手順では、カーネルバージョン部分は`uname -r`で取得していました。今回は異なるバージョンのLive DVDを使っているので手書きしました。  
  
```shell-session
cd /boot
cp initrd-2.6.18-308.el5PAE.img initrd-2.6.18-308.el5PAE.img.bak
mkinitrd -f -v /boot/initrd-2.6.18-308.el5PAE.img 2.6.18-308.el5PAE
```  
  
再起動します。  
  
```shell-session
exit
reboot
```  
  
## ステップ7 Linux Integrationをアンインストール  
  
無事起動したら、Hyper-VのLinux Integrationをアンインストールします。  
  
## ステップ8 VMware Toolsをインストール  
  
いつもの手順でVMware Toolsをインストールします。  
  
以上です。お疲れ様でした。 :smile:   
  
# 落穂ひろい: thick仮想ディスクをthinにする  
  
ESXiホストマシンへsshでログインして、vmkfstoolsを使います。  
  
```shell-session
vmkfstools -i original.vmdk new.vmdk -d 'thin'
```  
  
`ls -l`で見える数字は仮想ディスク全体のサイズなので、`-s`を付けると実サイズが見えます。  
  
こういう作業は、sshしてするよりvSphere PowerCLI使ったほうがよさげです。  
  
