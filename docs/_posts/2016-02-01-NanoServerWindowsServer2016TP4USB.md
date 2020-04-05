---
date: '2016-02-01T14:56:38+09:00'
layout: post
published: true
qiita_article_id: 900769eb54cfbe3272ff
tags:
- Hyper-V
- WindowsServer
- nanoserver
title: NanoServer (Windows Server 2016 TP4)をUSBメモリにインストールする
updated: '2016-02-02T10:46:24+09:00'

---
# 概要  
  
http://www.aka.ms/nanoserver に書かれている手順に従って、NanoServerをUSBメモリにインストールしてみました。  
  
# 必要なもの  
  
- Windows Server 2016 TP4のISOイメージ[^download]  
- USBメモリ (4GBくらいあれば足りそう)  
  
[^download]: https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-technical-preview からダウンロードできます。  
  
  
# 準備  
  
Windows Server 2016 Technical Preview 4のISOイメージをマウントしておきます。  
  
USBメモリをNTFSでフォーマットしておきます。アロケーションユニットサイズはデフォルト4096バイトです。  
  
  
# 手順  
  
## ステップ1: ISOイメージからファイルをコピーする  
  
ISOイメージの中にある`NanoServer\Convert-WindowsImage.ps1`ファイルと`NanoServer\NanoServerImageGenerator.psm1`ファイルをHDDの適当な場所へコピーします。  
  
自分はE:\NanoServerへコピーしました。  
  
## ステップ2: PowerShellモジュールを読み込む  
  
PowerShellを管理者権限で起動し、先ほどコピーしたPowerShellモジュールを読み込みます。  
  
****  
```ps1:
PS E:\NanoServer> Import-Module .\NanoServerImageGenerator.psm1 -Verbose
VERBOSE: Loading module from path 'E:\NanoServer\NanoServerImageGenerator.psm1'.
VERBOSE: Importing function 'Edit-NanoServerImage'.
VERBOSE: Importing function 'Get-NanoServerPackages'.
VERBOSE: Importing function 'New-NanoServerImage'.
```  
  
### モジュールの中身  
  
ちなみに、モジュールの中にはこんなコマンドがはいっています。  
  
****  
```ps1:
PS E:\NanoServer> Get-Command -Module NanoServerImageGenerator

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Function        Edit-NanoServerImage                               0.0        NanoServerImageGenerator
Function        Get-NanoServerPackages                             0.0        NanoServerImageGenerator
Function        New-NanoServerImage                                0.0        NanoServerImageGenerator
```  
  
## ステップ3: VHDファイルを作成する  
  
New-NanoServerImageコマンドレットを使用して、VHDファイルを作成します。  
  
****  
```ps1:
PS E:\NanoServer> New-NanoServerImage -MediaPath f:\ -BasePath .\base\ -TargetPath .\vhd\nanosvr.vhd -ComputerName nanotest -OEMDrivers -Compute -Containers -Language en-us

cmdlet New-NanoServerImage at command pipeline position 1
Supply values for the following parameters:
AdministratorPassword: *******

Windows(R) Image to Virtual Hard Disk Converter for Windows(R) 10
Copyright (C) Microsoft Corporation.  All rights reserved.
Version 10.0.10586.0.amd64fre.th2_release.151029-1700

INFO   : Looking for the requested Windows image in the WIM file
INFO   : Image 1 selected (ServerTuva)...
INFO   : Creating sparse disk...
INFO   : Attaching VHD...
INFO   : Initializing disk...
INFO   : Creating single partition...
INFO   : Formatting windows volume...
INFO   : Windows path (H:) has been assigned.
INFO   : System volume location: H:
INFO   : Applying image to VHD. This could take a while...
INFO   : Image was applied successfully.
INFO   : Making image bootable...
INFO   : Fixing the Device ID in the BCD store on VHD...
INFO   : Drive is bootable.  Cleaning up...
INFO   : Closing VHD...
INFO   : Closing Windows image...
INFO   : Done.
Done. The log is at: C:\Users\us1\AppData\Local\Temp\NanoServerImageGenerator.log
```  
  
指定したオプションの意味は下記のとおりです。  
  
- **MediaPath** ISOイメージをマウントしたドライブへのパスです  
- **BasePath** WIMファイルとパッケージファイルのコピー先です。  
- **TargetPath** VHDのファイル名です。拡張子vhdxにするとUEFI用らしいです。  
- **ComputerName** これから作成するコンピュータの名前です。  
- **OEMDrivers** 一般的なドライバを入れます。  
- **Compute** Hyper-Vロールを追加します。  
- **Containers** Windows Containersロールを追加します。  
- **Language** パッケージの言語です。日本語環境でこの作業をしているときは、en-usを指定します。  
  
### できあがったファイル  
  
baseに指定したディレクトリとtargetpathに指定したディレクトリにはこんなファイルができていました。  
  
****  
```ps1:
PS E:\NanoServer> ls base


    Directory: E:\NanoServer\base


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----       2016/02/01     11:41                Packages
d-----       2016/02/01     11:40                Tools
-a----       2016/02/01     11:41      641728512 base.vhdx
-ar---       2015/10/30     19:19      136954140 NanoServer.wim
```  
  
****  
```ps1:
PS E:\NanoServer> ls vhd


    Directory: E:\NanoServer\vhd


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       2016/02/01     11:45      669679616 nanosvr.vhd
```  
  
  
## ステップ4: USBメモリのbootsectを更新  
  
ここではUSBメモリはZ:ドライブです。  
  
****  
```ps1:
PS E:\NanoServer> bootsect.exe /nt60 z: /force /mbr
Target volumes will be updated with BOOTMGR compatible bootcode.

Z: (\\?\Volume{5c881b51-b59f-11e5-9bf4-6c626de1b411})

    Successfully updated NTFS filesystem bootcode.

\??\PhysicalDrive4

    Successfully updated disk bootcode.

Bootcode was successfully updated on all targeted volumes.
```  
  
## ステップ5: VHDファイルをUSBメモリへコピー  
  
できあがったVHDファイルをUSBメモリへコピーします。  
  
****  
```ps1:
PS E:\NanoServer> copy .\vhd\nanosvr.vhd z:\
```  
  
## ステップ6: bcdstoreを作成  
  
VHDファイルをマウントします。  
  
```
PS E:\NanoServer> diskpart

Microsoft DiskPart version 10.0.10240

Copyright (C) 1999-2013 Microsoft Corporation.
On computer: CPA647X

DISKPART> select vdisk file=z:\nanosvr.vhd

DiskPart successfully selected the virtual disk file.

DISKPART> attach vdisk

  100 percent completed

DiskPart successfully attached the virtual disk file.

DISKPART> exit

Leaving DiskPart...
```  
  
ここではH:ドライブにマウントされました。  
  
bcdbootコマンドでbcdstoreを作成します。  
  
```
PS E:\NanoServer> bcdboot h:\Windows /s z:
Boot files successfully created.
```  
  
## **2016/2/2追記** ステップ7: ファイアウォールを無効にする  
  
VHDの中にSetupComplete.cmdファイルを作成し、その中でファイアウォールを無効にします。  
  
```
PS E:\NanoServer> md h:\Windows\Setup\Scripts
PS E:\NanoServer> notepad h:\Windows\Setup\Scripts\SetupComplete.cmd
```  
  
SetupComplete.cmdの中身はこんなんです。  
  
****  
```bat:
netsh advfirewall set domainprofile state off
netsh advfirewall set privateprofile state off
netsh advfirewall set publicprofile state off
netsh advfirewall set currentprofile state off
```  
  
  
作業はこれでおしまいです。  
  
VHDファイルのマウントを外し、USBメモリを取り出します。  
  
お疲れ様でした。 :smile:   
  
  
