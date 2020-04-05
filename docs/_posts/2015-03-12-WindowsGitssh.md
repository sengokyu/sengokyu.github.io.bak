---
date: '2015-03-12T12:14:33+09:00'
layout: post
published: true
qiita_article_id: 86752e86388d9389a196
tags:
- Git
title: Windows版Gitで、直接接続できないリモートリポジトリにssh踏み台サーバ経由でアクセスする
updated: '2015-03-12T12:18:41+09:00'

---
すでに同内容記事多数なのですが、自分用メモとして記載します。  
お目汚し失礼。  
  
# 環境   
  
ネットワーク的には下記のようになっています。  
リポジトリが置いてある``repo-server``には、``fumidai``からしかsshログインできません。  
  
![Untitled.png](/assets/images/cc9c32d5-7a14-c9fe-f883-c81b12c9c13d.png)  
  
- ローカルWindows PC（Windows 8.1)  
- ``fumidai`` 懐かしのTurboLinux  
- ``repo-server`` CentOS  
- Windows版Git git version 1.9.5.msysgit.0  
  
# 準備  
  
## WindowsへGitをインストール  
  
[chocolatey](https://chocolatey.org/)を使うと簡単です。  
chocolateyをインストールしたら、Admin権限のあるコマンドプロンプトで``choco install git``と実行するだけです。  
  
## 踏み台サーバへconnectをインストール  
  
踏み台サーバは懐かしのTurboLinuxのため、ncもconnectも入っていませんでした。  
  
[connect.c](https://bitbucket.org/gotoh/connect/wiki/Home) から``connect.c``をダウンロードします。  
  
ダウンロードしたら踏み台サーバにコピーしてコンパイルします。  
``gcc -O2 connect.c -o connect``  
  
コンパイル成功したら適当なディレクトリにコピーします。  
``cp connect ~/bin/``  
  
## Windows環境変数を設定  
  
Windowsの環境変数``HOME``を設定します。値は``%USERPROFILE%``にします。ここに``.ssh``ディレクトリを作成します。  
  
![homeenv.png](/assets/images/d98e5c7a-5c52-6b43-9fdb-29707488710e.png)  
  
# sshを設定  
  
## ssh設定ファイルを作成  
  
コマンドプロンプトを起動して、.sshディレクトリを作成します。  
``mkdir %HOME%\.ssh``  
  
``config``ファイルを作成します。  
``notepad %HOME%\.ssh\config``  
  
内容は次のようになります。  
  
**config**  
```text:config
Host fumidai
  User sengoku
  Hostname fumidai.local

Host repo-server
  User guest001
  Hostname repo-server.example.com
  ProxyCommand "C:\Program Files (x86)\Git\bin\ssh.exe" fumidai /home/sengoku/bin/connect %h %p 
```  
  
最初の``Host``は``fumidai``へログインするための設定です。ユーザ名はsengoku、ホスト名はfumidai.localです。ユーザ名やホスト名は適宜変更してください。  
  
2番目の``Host``は``repo-server``へログインするための設定です。ユーザ名はguest001、ホスト名はrepo-server.example.comです。  
``fumidai``上で``/home/sengoku/bin/connect``コマンドを起動し、プロキシ動作させています。  
ユーザ名やホスト名は適宜変更してください。  
  
## sshの設定を確認  
  
sshの設定が正しくできたか、まずは普通にsshしてみます。  
  
コマンドプロンプトからsshを起動し``fumidai``へログインしてみます。  
``"C:\Program Files (x86)\Git\bin\ssh.exe" fumidai``  
引数の``fumidai``はHostに記述したものです。  
  
うまくいったらリモートにもログインしてみます。  
``"C:\Program Files (x86)\Git\bin\ssh.exe" repo-server``  
  
うまくいかないときは、sshに``-v``オプションをつけてdebug情報を見てみてください。  
  
# リモートリポジトリにアクセス  
  
準備が整いましたので、リモートリポジトリからソースを取得します。  
  
``git clone ssh://repo-server/~/repos/myproj.git``  
  
ここではリポジトリは``~/repos/myproj.git``にあります。適宜変更してください。  
  
お疲れ様でした。  
  
  
# 参考URL  
  
- [sshで多段接続 (ncも-Wもない人用)](http://auewe.hatenablog.com/entry/2014/03/10/043030)  
- [connect を使って簡単に多段 SSH を実現する方法](http://cl.pocari.org/2006-09-04-2.html)  
