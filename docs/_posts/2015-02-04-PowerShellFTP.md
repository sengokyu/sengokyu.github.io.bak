---
date: '2015-02-04T09:39:29+09:00'
layout: post
published: true
qiita_article_id: d214e7a8d1e399b42b18
tags:
- PowerShell
title: PowerShellでFTPサーバのファイルを一覧する
updated: '2015-02-04T09:39:29+09:00'

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
