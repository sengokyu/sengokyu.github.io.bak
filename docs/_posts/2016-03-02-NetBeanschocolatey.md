---
date: '2016-03-02T10:09:55+09:00'
layout: post
published: true
qiita_article_id: 542e8ec6bf803e6a4037
tags:
- NetBeans
- PowerShell
- chocolatey
title: 【トラブル】NetBeansのインストーラが作成する不正なレジストリ値のため、chocolateyのアンインストール操作が失敗する
updated: '2016-03-02T10:09:55+09:00'

---
# 現象  
  
chocolateyでなんらかのアプリをアンインストールしようとしたり、PowerShellで `Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*` なんて操作をすると、 `Specified cast is not valid.` というメッセージとともに例外が発生します。  
  
****  
```ps1:
PS C:\Windows\system32> Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\* | Format-Table -Aut
oSize

DisplayName DisplayVers DisplayIcon InstallLoca UninstallSt    NoModify    NoRepair EstimatedSi VersionMajo VersionMino
            ion                     tion        ring                                         ze           r           r
----------- ----------- ----------- ----------- -----------    --------    -------- ----------- ----------- -----------
7-Zip 15... 15.14       C:\Progr... C:\Progr... C:\Progr...           1           1        4843          15          14

Firefox ... 41.0a2      C:\Progr... C:\Progr... "C:\Prog...           1           1       98662
Git vers... 2.7.1       C:\Progr... C:\Progr... "C:\Prog...           1           1      189124
Microsof... 1.1.40219   C:\Windo... C:\Progr... C:\Progr...                               22174           1           1
Microsof...
Microsof...             "C:\Prog...             "C:\Prog...
Microsof...             C:\Windo... C:\Windo... C:\Windo...                               88724           2           0
Microsof... 10.0.50903  msiexec.exe C:\Progr... C:\Progr...           0                   10805           4
Get-ItemProperty : Specified cast is not valid.
At line:1 char:1
+ Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Unin ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Get-ItemProperty], InvalidCastException
    + FullyQualifiedErrorId : System.InvalidCastException,Microsoft.PowerShell.Commands.GetItemPropertyCommand
```  
  
  
同じ問題に遭遇してるひと → https://github.com/PSAppDeployToolkit/PSAppDeployToolkit/issues/102  
  
  
# 原因  
  
NetBeansインストーラが作成する不正なDWORD値を読み込めないのが原因です。  
  
バグ報告 → https://netbeans.org/bugzilla/show_bug.cgi?id=251538  
  
  
# 回避策  
  
NetBeansをアンインストールします。  
  
