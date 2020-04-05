---
date: '2019-12-28T09:37:36+09:00'
layout: post
published: true
qiita_article_id: 49ee1a35b88b65936d90
tags:
- Windows
- Windows10
title: '[Microsoft] 消してしまった Microsoft Store アプリを復活（再インストール）する方法'
updated: '2019-12-28T09:37:36+09:00'

---
# これはなに？  
  
Microsoft Storeを使ってインストールしたアプリを復活する方法ではなく、「Microsoft Store」アプリそのものを復活する方法です。  
  
# はじめに  
  
Windows 10を新規購入したりインストールしたりすると、最初からアプリが色々インストールされています。  
近頃は半分AdWareのようなゲームも多くて、なんだかなぁな気分です。  
  
ストアアプリ多いなー。全部消しとこ  
↓  
↓  
PowerShellを起動してっと  
  
```powershell
Get-AppXPackage | Remove-AppXPackage
```  
  
これでよし。  
↓  
↓  
（後日）  
↓  
↓  
このアプリ入れるの？いいよ。  
えっ、Microsoft Storeから入れないとだめなの！？   <<<===  イマココ！  
  
# やりかた  
  
こちらに書いてありました。ありがたいことです。  
  
https://www.winhelponline.com/blog/restore-windows-store-windows-10-uninstall-with-powershell/  
  
```powershell
Get-AppxPackage -allusers Microsoft.WindowsStore | % {Add-AppxPackage -Register "$($_.InstallLocation)\AppXManifest.xml"}
```  
  
`Get-AppXPacage -AllUsers Microsoft.WindowsStore`で実態としては消えていないMicrosoft Storeアプリを取得し、マニフェストファイルの場所を得ています。そこから`Add-AppXPackage`で再インストールしています。  
  
ちなみに  
  
```powershell
Get-AppXPackage -AllUsers Microsoft.WindowsStore | Add-AppXPackage
```  
  
という書き方はエラーになります。  
  
