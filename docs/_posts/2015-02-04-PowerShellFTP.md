---
'created_at: ': '2015-02-04T09:39:29+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: PowerShell
      versions: []
  state: 0
title: "PowerShell\u3067FTP\u30B5\u30FC\u30D0\u306E\u30D5\u30A1\u30A4\u30EB\u3092\u4E00\
  \u89A7\u3059\u308B"
'updated_at: ': '2015-02-04T09:39:29+09:00'

---
.Netの```System.Net.WebRequest```を使用します。  
  
**lsftp.ps1**  
```ps1:lsftp.ps1
# 一覧を取得するURL
$url = "ftp://ftp.example.com/pub"

# ログインID
$user = "someone"

# パスワード
$pass = "secret"

$wr = [System.Net.WebRequest]::Create($url)
$wr.Credentials = New-Object System.Net.NetworkCredential($user, $pass)
$wr.Method = [System.Net.WebRequestMethods+Ftp]::ListDirectoryDetails
$res = $wr.GetResponse()
$rs = New-Object System.IO.StreamReader($res.GetResponseStream())
$list = $rs.ReadToEnd()
$rs.Close()
$res.Close()

# $listにファイルの一覧が格納されますので、あとは煮るなり焼くなり。
```  
