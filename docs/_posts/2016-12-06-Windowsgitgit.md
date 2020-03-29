---
'created_at: ': '2016-12-06T16:14:49+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Windows
      versions: []
    - name: Git
      versions: []
  state: 0
title: "[Windows][git] git\u30C7\u30FC\u30E2\u30F3\u3092\u30B5\u30FC\u30D3\u30B9\u3068\
  \u3057\u3066\u52D5\u304B\u3059"
'updated_at: ': '2016-12-06T16:14:49+09:00'

---
Windowsでｇｉｔデーモンを動かし、git://プロトコルでアクセスできるようにする手順です。  
  
クライアントがWindowsだけならば、ファイル共有へレポジトリを作ればよいのですが、Windows以外もいるのでこんな手順にしました。  
  
# 準備  
  
## Chocolateyをインストールする  
  
いろいろ簡単にインストールするために、[Chocolatey](https://chocolatey.org/)をインストールします。  
  
## Git for Windowsとnssmをインストールする  
  
gitとnssmをインストールします。  
  
nssmは任意のexeをサービス化できるツールです。  
  
```
C:\> choco install git nssm
```  
  
  
# gitデーモンを動かす手順  
  
## 手順 1 レポジトリ置き場を作る  
  
公開するレポジトリを置くディレクトリを作成します。  
  
```
C:\> mkdir h:\export\repos
```  
  
## 手順 2 gitデーモンをサービスとして登録する  
  
```
C:\>nssm install git-daemon "c:\Program Files\Git\cmd\git.exe" "daemon --export-all --enable=receive-pack --base-path=h:\export\repos"
```  
  
## 手順 3 ファイアウォールでポートを開放する  
  
TCP 9418番ポートを開放してアクセスできるようにします。  
  
# レポジトリを作成する手順  
  
レポジトリに書き込みできるようにするためには、ちょっとひと手間必要です。  
  
## レポジトリを作成  
  
これはいつもどおりです。  
  
```
C:\> git init --bare h:\export\repos\test.git
```  
  
## 空ファイルを作成  
  
このファイルがないとpushできないです。  
  
```
C:\> echo > h:\export\repos\test.git\git-daemon-export-ok
```  
