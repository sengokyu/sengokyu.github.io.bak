---
date: '2015-07-10T18:12:15+09:00'
layout: post
published: true
qiita_article_id: 3ef87c6af0c1b0441f09
tags:
- PowerShell
- Putty
title: PowerShellからputtyのscpコマンドを使用してファイルをアップロードする
updated: '2015-07-13T09:16:50+09:00'

---
PowerShellから外部コマンド（puttyのpscp）を呼び出して、リモートへファイルをコピーします。  
  
# 使い方 #  
  
  
**usage.ps1**  
```ps1:usage.ps1
Set-StrictMode -Version Latest

# プログラム本体ファイルの読みこみ
. "UploadFile.ps1"

# pscpコマンドへのパス
[string] $ScpCmd = "C:\opt\putty\pscp.exe" 

# sshサーバのホスト名
[string] $SshHostname = "myserver" 

# コピー先のパス
[string] $DstPath = "tmp/upload/"

# pscpのコマンドラインオプション
[string] $ScpOptions = "-q -batch -l myname -i `"C:\Users\myname\AppData\putty\id_rsa.ppk`"" 

# コピー元ファイル名
[string[]] = $srcfiles = @(
 "file1"
 "file2"
 "file3") 

$srcfiles | UploadFile -ScpCmd $ScpCmd -ScpOptions $ScpOptions -SshHostname $SshHostname -DstPath $DstPath

```  
  
# プログラム本体 #  
  
**UploadFile.ps1**  
```ps1:UploadFile.ps1
Set-StrictMode -Version Latest

function UploadFile([string]$ScpCmd, [string]$ScpOptions, [string]$SshHostname, [string]$DstPath) {
<#
.SYNOPSIS
pcscpコマンドを使用してファイルをアップロードします。
# >
    begin {
        [string[]] $ScpOptionsArray = $ScpOptions -split " "
    }

    process {
        Write-Debug "Uploading '${_}' to ${SshHostname}:${DstPath}."
        "Uploading '${_}' to ${SshHostname}:${DstPath}."

        [string[]] $args = $ScpOptionsArray
        $args += "`"$_`""
        $args += "${SshHostname}:${DstPath}"

        Write-Debug "Executing $ScpCmd"
        Write-Debug ($args -join " ")

        # Start-Process cmdletはERRORLEVELを取得できないのでProcessStartInfoを使う
        $pinfo = New-Object System.Diagnostics.ProcessStartInfo
        $pinfo.FileName = $ScpCmd
        $pinfo.RedirectStandardOutput = $true
        $pinfo.RedirectStandardError = $true
        $pinfo.Arguments = $args
        $pinfo.UseShellExecute = $false

        $proc = New-Object System.Diagnostics.Process
        $proc.StartInfo = $pinfo
        [void]$proc.Start()
        $proc.WaitForExit()
        $stdout = $proc.StandardOutput.ReadToEnd()
        $stderr = $proc.StandardError.ReadToEnd()

        if (-not [String]::IsNullOrEmpty($stdout)) {
            $stdout
        }

        if (-not [String]::IsNullOrEmpty($stderr)) {
            $stderr
        }

        Write-Debug "Command exit code: $($proc.ExitCode)"

        if ($proc.ExitCode -ne 0) {
            throw "${_}: Upload failed."
        }
    }
}

```  
