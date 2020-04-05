---
date: '2016-01-08T15:26:31+09:00'
layout: post
published: true
qiita_article_id: 1e4877c10f718bca8c46
tags:
- Windows
- Microsoft
- Hyper-V
title: Hyper-V Server 2012 R2をUSBメモリにインストールする
updated: '2016-01-20T09:32:30+09:00'

---
# 概要  
  
USBメモリから起動するHyper-V Server 2012 R2を用意しました。  
  
作業手順は https://technet.microsoft.com/en-us/library/ee731893%28WS.10%29.aspx をなぞりました。  
  
以下長々と書いておりますが、手順としてはこれだけです。  
  
1. 仮想ディスクを作成してHyper-Vのインストールイメージを展開  
2. USBメモリのbootcodeを更新し、BCDSTOREを作成  
  
# 必要なもの  
  
 - USBメモリ(12GB以上)  
 - （Windows 7以前の場合)Windows ADK [^1]  
 - Hyper-V Server 2012 R2 WIMファイル [^2]  
  
  
----  
  
■ （2016/1/20追記）  Windows 8以降の場合は、付属のコマンドがあるのでWindows ADK不要だそうです。Thanks! @Netwanderer   
  
----  
  
  
[^1]: https://msdn.microsoft.com/ja-jp/windows/hardware/dn913721.aspx からダウンロードできます  
[^2]: ISOファイルの中に入っています。ISOファイルは https://technet.microsoft.com/ja-jp/evalcenter/dn205299.aspx からダウンロードできます  
  
# 事前準備  
  
1. Windows 7以前の場合、Windows ADKをインストールします。必要なファイルをダウンロードするので時間がかかります。  
  
  
# 作業手順  
  
## ステップ1: USBメモリのフォーマット  
  
USBメモリをフォーマットします。正しいディスク番号を選択するように注意してください。  
  
ここではZ:ドライブに割り当てました。  
  
```
C:\>diskpart

Microsoft DiskPart version 10.0.10240

Copyright (C) 1999-2013 Microsoft Corporation.
On computer: xxxxxxx

DISKPART> list disk

  Disk ###  Status         Size     Free     Dyn  Gpt
  --------  -------------  -------  -------  ---  ---
  Disk 0    Online          111 GB      0 B
  Disk 1    Online          223 GB      0 B        *
  Disk 2    Online          465 GB  1024 KB        *
  Disk 3    Online          100 GB      0 B
  Disk 4    Online           12 GB      0 B
  Disk 5    Online         7635 MB      0 B

DISKPART> select disk 5

Disk 5 is now the selected disk.

DISKPART> clean

DiskPart succeeded in cleaning the disk.

DISKPART> create partition primary

DiskPart succeeded in creating the specified partition.

DISKPART> select partition 1

Partition 1 is now the selected partition.

DISKPART> active

DiskPart marked the current partition as active.

DISKPART> format quick fs=ntfs

  100 percent completed

DiskPart successfully formatted the volume.

DISKPART> assign letter=z

DiskPart successfully assigned the drive letter or mount point.

DISKPART> exit

Leaving DiskPart...
```  
  
## ステップ2: VHDファイルの作成  
  
USBメモリ上にサイズ固定のVHDファイルを作成し、NTFSでフォーマットします。  
  
続く作業のためにR:ドライブに割り当てます。  
  
  
```
DISKPART> create vdisk file=z:\hyperv.vhdx maximum=12288 type=fixed

  100 percent completed

DiskPart successfully created the virtual disk file.

DISKPART> select vdisk file=z:\hyperv.vhdx

DiskPart successfully selected the virtual disk file.

DISKPART> attach vdisk

  100 percent completed

DiskPart successfully attached the virtual disk file.

DISKPART> create partition primary

DiskPart succeeded in creating the specified partition.

DISKPART> assign letter=r

DiskPart successfully assigned the drive letter or mount point.

DISKPART> format quick fs=ntfs label=hyperv

  100 percent completed

DiskPart successfully formatted the volume.

DISKPART> exit

Leaving DiskPart...
```  
  
## ステップ3: VHDへWIMを展開  
  
下記はWIMファイルがf:\sources\install.wimにある場合です。  
  
### Windows 7以前の場合  
  
Windows ADKのimagexツールを使ってHyper-VのWIMファイルを作成したVHDへ展開します。  
  
```
C:\>"c:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Deployment Tools\amd64\DISM\imagex.exe" /apply f:\sources\install.wim 1 r:

ImageX Tool for Windows
Copyright (C) Microsoft Corp. All rights reserved.
Version: 10.0.10011.16384


[ 100% ] Applying progress

Successfully applied image.

Total elapsed time: 7 min 40 sec
```  
  
### Windows 8以降の場合  
  
Windows付属のDISMコマンドを使ってHyper-VのWIMファイルを作成したVHDへ展開します。  
  
```
C:\>dism /apply-image /imagefile:f:\sources\install.wim /index:1 /applydir:r:\

Deployment Image Servicing and Management tool
Version: 10.0.10240.16384

Applying image
[==========================100.0%==========================]
The operation completed successfully.
```  
  
## ステップ4: bootcodeを更新  
  
Windows ADKまたはWindows付属のbootsectコマンドを使ってUSBメモリのbootcodeを更新します。  
  
下記はWindows ADK付属のコマンドを使う場合です。  
  
```
C:\>"c:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Deployment Tools\amd64\BCDBoot\bootsect.exe" /nt60 z: /force /mbr
Target volumes will be updated with BOOTMGR compatible bootcode.

Z: (\\?\Volume{5c881b51-b59f-11e5-9bf4-6c626de1b411})

    Forced dismount complete, open handles to this volume are now invalid.

    Successfully updated NTFS filesystem bootcode.

\??\PhysicalDrive4

    Successfully updated disk bootcode.

Bootcode was successfully updated on all targeted volumes.
```  
  
## ステップ5: USBメモリにBCDSTOREを作成  
  
前ステップで仮想HDDのマウントが外れてしまったので再度マウント  
  
```
DISKPART> select vdisk file=z:\hyperv.vhdx

DiskPart successfully selected the virtual disk file.

DISKPART> attach vdisk

  100 percent completed
```  
  
BCDSTOREを作成します。  
  
```
C:\>bcdboot r:\Windows /s z:
Boot files successfully created.
```  
  
  
## ステップ6: ページスワップを無効にする  
  
仮想HDD内のレジストリハイブを読み込んで、ページスワップを無効にします。  
  
```
C:\>reg load HKLM\HyperVTemp r:\windows\system32\config\system 

C:\>reg add "hklm\HyperVTemp\ControlSet001\Control\Session Manager\Memory Management" /v PagingFiles /t REG_MULTI_SZ /d "" /f
The operation completed successfully.

C:\>reg delete "hklm\HyperVTemp\ControlSet001\Control\Session Manager\Memory Management" /v ExistingPageFiles /f
The operation completed successfully.

C:\>reg unload hklm\HyperVTemp
The operation completed successfully.
```  
  
## ステップ7: 再起動  
  
仮想HDDをディスマウントして再起動します。  
  
USBメモリから起動できるようになっていなければ、BIOS設定を変更します。  
  
# 最後に  
  
もしかすると rufus を使えば一発で作業が終わるかもです。  
  
rufusはこちら → http://rufus.akeo.ie/  
  
----  
  
