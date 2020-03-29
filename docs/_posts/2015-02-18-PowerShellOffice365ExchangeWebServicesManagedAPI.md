---
'created_at: ': '2015-02-18T18:15:24+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: PowerShell
      versions: []
    - name: Office365
      versions: []
    - name: Exchange
      versions: []
  state: 0
title: "PowerShell\u304B\u3089Office365\u306EExchange Web Services Managed API\u3092\
  \u4F7F\u7528\u3057\u3066\u30E1\u30FC\u30EB\u3092\u9001\u308B"
'updated_at: ': '2015-02-18T18:15:24+09:00'

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
