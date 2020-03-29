---
'created_at: ': '2016-03-02T10:09:55+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: NetBeans
      versions: []
    - name: PowerShell
      versions: []
    - name: chocolatey
      versions: []
  state: 0
title: "\u3010\u30C8\u30E9\u30D6\u30EB\u3011NetBeans\u306E\u30A4\u30F3\u30B9\u30C8\
  \u30FC\u30E9\u304C\u4F5C\u6210\u3059\u308B\u4E0D\u6B63\u306A\u30EC\u30B8\u30B9\u30C8\
  \u30EA\u5024\u306E\u305F\u3081\u3001chocolatey\u306E\u30A2\u30F3\u30A4\u30F3\u30B9\
  \u30C8\u30FC\u30EB\u64CD\u4F5C\u304C\u5931\u6557\u3059\u308B"
'updated_at: ': '2016-03-02T10:09:55+09:00'

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
  
