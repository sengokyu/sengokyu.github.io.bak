---
'created_at: ': '2016-04-01T12:00:10+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Microsoft
      versions: []
    - name: Azure
      versions: []
    - name: PowerShell
      versions: []
  state: 0
title: "PowerShell\u304B\u3089Microsoft Azure\u3092\u7BA1\u7406\u3059\u308B\u3010\u6E96\
  \u5099\u7DE8\u3011"
'updated_at: ': '2016-04-01T12:00:10+09:00'

---
Microsoft Azure管理ポータルには旧来のクラシック版と、新しいリソースマネージャ版があります。  
  
PowerShellにも新しい版があるようです。[Azure Resource Manager Cmdlets](https://msdn.microsoft.com/ja-jp/library/mt125356.aspx)にインストール方法がありました。  
  
# ステップ :one: Windows Management Framework 5.0をインストール  
  
Windows Management Framework 5.0がインストールされていなければ、ダウンロードしてインストールします。  
  
Windows 10のひとは不要です。  
  
https://www.microsoft.com/en-us/download/details.aspx?id=50395  
  
  
# ステップ :two: PowerShellモジュールをインストール  
  
管理者権限でPowerShellプロンプトを立ち上げます。  
  
インストールを実行します。  
  
****  
```ps1:
Install-Module AzureRM
Install-AzureRM
```  
  
# 結果  
  
PowerShell ISEを立ち上げると、こんな感じにコマンドが入っていました。  
  
![azurermcmdlet.JPG](/assets/images/9a0049c1-f211-5c62-2d72-a1955173bb3d.jpeg)  
