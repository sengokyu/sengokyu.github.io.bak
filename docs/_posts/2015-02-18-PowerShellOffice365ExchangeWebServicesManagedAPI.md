---
date: '2015-02-18T18:15:24+09:00'
layout: post
published: true
qiita_article_id: 07371a9bcf97b1b96cad
tags:
- PowerShell
- Office365
- Exchange
title: PowerShellからOffice365のExchange Web Services Managed APIを使用してメールを送る
updated: '2015-02-18T18:15:24+09:00'

---
Exchange Web Services Managed APIの練習です。  
Office365のExchange Web Services Managed APIをたたいてメールを送信します。  
  
PowerShellからメールを送信するだけならもっと簡単な方法がありますので、この内容を使うことはないかな。  
  
# 準備 #  
  
[Microsoft Exchange Web Services Managed API 2.2 ](http://www.microsoft.com/en-us/download/details.aspx?id=42951)をダウンロードしてインストールします。  
  
# ソース #  
  
someone@example.comあてに「Hello world」というサブジェクトのメールを送信します。  
  
**sendmail.ps1**  
```ps1:sendmail.ps1
Import-Module "$env:ProgramFiles\\Microsoft\\Exchange\\Web Services\\2.2\\Microsoft.Exchange.WebServices.dll"

# Office365に接続するログインIDとパスワードです
$user = "myname@myorg.apac.microsoftonline.com"
$pass = "mysecret"

# AutoDiscoveryURLのコールバックです。これがないとAutoDiscoveryURLで失敗します
# 引数として渡すために変数に保存しておきます
function RedirectionUrlValidationCallback($redirectionUrl) {
    return $true
}

$redirectionUrlValidationCallback = Get-Content Function:\RedirectionUrlValidationCallback

$cred = New-Object Microsoft.Exchange.WebServices.Data.WebCredentials($user, $pass)

$service = New-Object Microsoft.Exchange.WebServices.Data.ExchangeService
# トレースログを有効にします。
$service.TraceEnabled = $true
$service.TraceEnablePrettyPrinting = $true
$service.TraceFlags = [Microsoft.Exchange.WebServices.Data.TraceFlags]::All
$service.Credentials = $cred
# 接続するURLは必ずAutodiscoverする必要があります
$service.AutodiscoverUrl($user, $redirectionUrlValidationCallback)

$email = New-Object Microsoft.Exchange.WebServices.Data.EmailMessage($service)
$email.ToRecipients.Add("someonea@example.com") # メール宛先
$email.Subject="Hello world" # メールサブジェクト
$email.Body = New-Object Microsoft.Exchange.WebServices.Data.MessageBody("Hello EWS API") # メール本文
$email.Send()

```  
  
# はまったこと #  
  
EWS APIのURLは、auto discoveryしないとダメです。  
