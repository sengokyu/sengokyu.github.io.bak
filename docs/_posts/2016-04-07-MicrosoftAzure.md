---
date: '2016-04-07T20:55:42+09:00'
layout: post
published: true
qiita_article_id: 99b14b4705528580a97a
tags:
- Microsoft
- Azure
- PowerShell
title: 【再】[Microsoft Azure]勤務時間中だけ仮想マシンを動かす（スケジュールによる自動起動・停止）
updated: '2016-04-11T09:13:03+09:00'

---
以前に書いた記事[^1]は手順も長く面倒だったので、Moduleとしてまとめて[PowerShell Gallery](https://www.powershellgallery.com/)へ公開しました。  
  
[^1]: [[Microsoft Azure]勤務時間中だけ仮想マシンを動かす（スケジュールによる自動起動・停止）](http://qiita.com/sengoku/items/1c3994ac8a2f0f0e88c5)  
  
# 準備  
  
PowerShell Galleryを使用できるように、Windows 10かWMF 5を用意します。  
  
モジュールをインストールしてインポートします。  
  
```
PS C:\> Install-Module AzureRmMinus
PS C:\> Import-Module AzureRmMinus
```  
  
`Login-AzureRmAccount`でログインしておきます。  
  
# 使い方  
  
## ステップ:one:  
  
アプリケーション、サービスプリンシパル、Automationアカウント、RunBook、Automationアカウント資産を作成します。  
  
以前の記事の[ステップ:one:](http://qiita.com/sengoku/items/1c3994ac8a2f0f0e88c5#%E3%82%B9%E3%83%86%E3%83%83%E3%83%97-one-ad%E3%82%A2%E3%83%97%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%82%92%E4%BD%9C%E6%88%90) ～ [ステップ:seven:](http://qiita.com/sengoku/items/1c3994ac8a2f0f0e88c5#%E3%82%B9%E3%83%86%E3%83%83%E3%83%97-seven-runbook%E3%82%92%E7%99%BA%E8%A1%8C) までに相当します。  
  
****  
```ps1:
PS C:\> New-AzureRmStartStopVmAutomation -ResourceGroupName rg01 -AutomationAccountName aaaa01
AD Application's Password:: *********
Re-type AD Application's Password:: *********
```  
  
コマンドを起動するとパスワードを聞かれますので、アプリケーションに設定したいパスワードを入力してください。ここで設定するパスワードは他では使いませんので、適当に長く複雑なものを入力してください。  
  
ロケーションは指定されたリソースグループと同一になります。  
  
### オプションの説明  
  
| オプション | 説明 |  
|-----------|------|  
|ResourceGroupName | 既存のリソースグループ名です。 |  
|AutomationAccountName | 作成するAutmationアカウント名です。 |  
  
  
## ステップ:two: スケジュールの作成  
  
作成したAutomationアカウントにスケジュールを作成します。  
  
****  
```ps1:
PS C:\> New-AzureRmAutomationSchedule -ResourceGroupName rg01 -AutomationAccountName aaaa01 -StartTime "2016-04-08 9:00" -Name nineo -OneTime
```  
  
ここで作ったスケジュール名は次のステップで使います。  
  
## ステップ:three: スケジュールとRunBookを紐づけ  
  
VM起動RunBookとスケジュールを紐づけるときは、`Register-AzureRmStartVmScheduleRunbook`を使います。  
  
****  
```ps1:
PS C:\> Register-AzureRmStartVmScheduleRunbook -ResourceGroupName rg01 -AutomationAccountName aaaa01 -VmName MyVmName -ScheduleName nineo
```  
  
VM停止RunBookとスケジュールを紐づけるときは、`Register-AzureRmStopVmScheduleRunbook`を使います。  
  
****  
```ps1:
PS C:\> Register-AzureRmStopVmScheduleRunbook -ResourceGroupName rg01 -AutomationAccountName aaaa01 -VmName MyVmName -ScheduleName nineo
```  
  
  
### オプションの説明  
  
| オプション | 説明 |  
|-----------|------|  
|ResourceGroupName | 既存のリソースグループ名です。 |  
|AutomationAccountName | 作成したAutmationアカウント名です。 |  
|VmName | 仮想マシン名です。 |  
|ScheduleName | 作成したスケジュール名です。 |  
  
