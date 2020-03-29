---
'created_at: ': '2019-12-28T09:37:36+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Windows
      versions: []
    - name: Windows10
      versions: []
  state: 0
title: "[Microsoft] \u6D88\u3057\u3066\u3057\u307E\u3063\u305F Microsoft Store \u30A2\
  \u30D7\u30EA\u3092\u5FA9\u6D3B\uFF08\u518D\u30A4\u30F3\u30B9\u30C8\u30FC\u30EB\uFF09\
  \u3059\u308B\u65B9\u6CD5"
'updated_at: ': '2019-12-28T09:37:36+09:00'

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
  
