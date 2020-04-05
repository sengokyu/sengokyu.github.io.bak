---
date: '2020-03-31T17:40:23+09:00'
layout: post
published: true
qiita_article_id: 66cd44aab8ad541116ae
tags:
- Windows
- PowerShell
title: '[Microsoft] 空フォルダを全削除するPowerShellスクリプト - Remove empty folders recursively. '
updated: '2020-04-03T18:18:52+09:00'

---
robocopy /purgeは、ファイルは消してくれるけれどディレクトリは消してくれません。  
アプリをアンインストールするとAppDataの下に空フォルダが残ります。  
それらを一括して消したいとき用のスクリプトです。  
  
深さ優先で消してますので、消し残しありません。  
  
**4/3 追記**  
初出時は、隠しディレクトリ/ファイルを消せていませんでした。  
  
# スクリプト本体  
  
```powershell
Function Remove-EmptyFolder {
    [cmdletbinding()]
    Param(
        [parameter(mandatory=$true)][String]$Path
    )

    Process {
        (Get-ChildItem -Force $Path | ? { $_.PSIsContainer -eq $true }) | % { Remove-EmptyFolder $_.FullName }
                
        $dir = Get-Item -Force $Path
       
        if ($dir.PsIsContainer -and $dir.GetFileSystemInfos().Count -eq 0) {
            Remove-Item -Force $dir
        }
    }
}
```  
  
`notepad $profile` としてファイルを開いてコピペしてあげれば、PowerShell起動時に読み込まれるようになります。  
  
  
# 使い方  
  
```powershell
Remove-EmptyFolder フォルダのパス
```  
  
  
