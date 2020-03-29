---
'created_at: ': '2015-07-10T18:12:15+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: PowerShell
      versions: []
    - name: Putty
      versions: []
  state: 0
title: "PowerShell\u304B\u3089putty\u306Escp\u30B3\u30DE\u30F3\u30C9\u3092\u4F7F\u7528\
  \u3057\u3066\u30D5\u30A1\u30A4\u30EB\u3092\u30A2\u30C3\u30D7\u30ED\u30FC\u30C9\u3059\
  \u308B"
'updated_at: ': '2015-07-13T09:16:50+09:00'

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
