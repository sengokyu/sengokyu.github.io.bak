---
'created_at: ': '2017-12-15T13:33:18+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Windows
      versions: []
    - name: Microsoft
      versions: []
    - name: PowerShell
      versions: []
  state: 0
title: "Windows Server 2016 Core\u30A4\u30F3\u30B9\u30C8\u30FC\u30EB\u3092PowerShell\u3067\
  \u8A2D\u5B9A\u3059\u308B"
'updated_at: ': '2017-12-15T13:33:18+09:00'

---
Windows Server 2016から、初期インストール時にあえて選ばないとGUIが付いてこないようになりました。  
RSATをインストールする前に、コマンドラインで少し頑張ってみました。  
  
  
# 初期設定  
  
`sconfig.cmd`で次の設定ができます。  
  
- ドメインへの参加  
- コンピュータ名  
- ローカル管理者の追加  
- リモート管理の有効化  
- Windows Updateの設定  
- リモートデスクトップの有効化  
- IPアドレス  
  
![image.png](/assets/images/23c08fe4-824a-4163-d7f2-dbe19eee3732.png)  
  
  
# プロキシの設定  
  
`netsh`コマンドを使います。  
  
```doscon
netsh winhttp set proxy my.proxy.server:8000 "localhost;<local>"
```  
  
:disappointed_relieved:  これでいいはずなのですが、`update-help`してもヘルプをダウンロードできない。。。  
  
  
# Windows役割/機能のインストール  
  
```powershell
Install-WindowsFeature 役割名
```  
  
役割名とインストール状況は`Get-WindowsFeature`でわかります。  
  
# PowerShell Remotingを有効にする  
  
```powershell
Enable-PSRemoting -Force
```  
  
## リモートマシンからPowerShellを使う  
  
```powershell
$cred = Get-Credential
Enter-PSSession -Credential $cred コンピュータ名
```  
  
Get-Credentialでは、リモートマシンへ接続するアカウントを入力します。  
  
  
# ドメインコントローラにする  
  
```powershell
$cred = Get-Credential
Install-ADDSDomainController -Credential $cred -InstallDns
```  
  
Get-Credentialでは、ドメイン管理者のアカウントを入力します。  
  
参照: https://docs.microsoft.com/en-us/powershell/module/addsdeployment/install-addsdomaincontroller  
  
  
# コマンドいろいろ  
  
| 内容 | コマンド |  
|:--|:--|  
| ログオフ  | `logoff`  |  
| 再起動  | `shutdown /t 0 /r`  |  
| シャットダウン  | `shutdown /t 0 /s`  |  
| タスクマネージャ | taskmgr |  
  
  
# 感想  
  
んn年前は、  
「unixむりー、WindowsみたくGUIがないと管理できないー」  
なんて言われることがよくありました。  
Windows ServerがCUIオンリーになったのは、もうGUIなくてもいいくらいにITリテラシーが向上したという理解でいいのかな？  
  
  
