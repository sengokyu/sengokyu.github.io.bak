---
date: '2016-02-17T10:54:21+09:00'
layout: post
published: true
qiita_article_id: c92056fc45bc3f64f4ae
tags:
- Windows
- Linux
title: '[rpm][yum][apt][chocolatey]パッケージ管理システムでよく使うコマンドまとめ'
updated: '2018-04-12T10:10:22+09:00'

---
複数のパッケージ管理システムを使っていると、こっちのシステムではどうやるんだっけ？  
ということがよくあります。  
そんなときに参照したい情報をまとめました。  
  
  
| やりたいこと | yum/rpm | apt | chocolatey |  
|:----------|:----|:----|:-----------|  
| パッケージのインストール | `yum install ` *パッケージ名* | `apt-get install ` *パッケージ名* | `choco install ` *パッケージ名*  
| パッケージのアンインストール | `yum remove ` *パッケージ名* | `apt-get remove ` *パッケージ名* | |  
| 全パッケージの更新 | `yum update` | `apt-get upgrade` | `choco upgrade all` |  
| インストール済みパッケージの一覧 | `yum list installed` または `rpm -qa` | `apt list --installed` | `choco list -l` |  
| あるファイルが属するパッケージを調べる | `rpm -qf` *ファイル名* | `dpkg-query --search` *ファイル名* または `apt-file search` *ファイル名*  | |  
| パッケージを探す | `yum search ` *キーワード* | `apt search ` *キーワード* | |  
| パッケージの情報 | `yum info ` *パッケージ名* | `apt show ` *パッケージ名* | |  
| パッケージに含まれるファイルの一覧 | `rpm -ql ` *パッケージ名* | `dpkg-query --listfiles` *パッケージ名* または `apt-file list ` *パッケージ名* | |  
  
  
apt-fileコマンドは、apt-fileパッケージに入っています。  
