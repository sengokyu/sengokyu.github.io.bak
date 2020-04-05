---
date: '2015-09-10T20:57:11+09:00'
layout: post
published: true
qiita_article_id: 21e0939a0044af165504
tags:
- Mac
- MovableType
title: mongoose Webサーバを使ってサクッとMovableTypeを動かす。Yosemiteで。
updated: '2015-09-10T20:57:11+09:00'

---
ちょろっとだけMovableTypeを動かしたかったのです。その記録です。  
  
  
  
# 必要なもの  
  
## Xcode  
  
Xcodeがインストールされていなければ、AppStoreからXcodeをインストールしてください。  
  
## Homebrew  
  
MacOS X向けパッケージマネージャです。monngooseのインストールに使います。  
  
インストールされていなければ、[HomebrewのWebサイト](http://brew.sh)に書かれているやり方に従ってインストールしてください。  
  
ターミナルを起動して以下をコピペするだけです。  
  
****  
```sh:
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```  
  
## cpanminus  
  
CPANモジュールのインストールに使います。  
  
インストールされていなければ、[cpanminusのREADME](https://github.com/miyagawa/cpanminus)に書かれているやり方に従ってインストールしてください。  
  
ターミナルを起動して以下をコピペするだけです。複数人で使っているMacなので、ホームディレクトリにインストールしました。  
  
****  
```sh:
curl -L https://cpanmin.us | perl - App::cpanminus
```  
  
## MovableTypeのzipファイル  
  
[MovableType](http://www.movabletype.jp/opensource/)からダウンロードしておきます。  
  
  
  
  
# mongoose、MySQLをインストール  
  
Homebrewを使用してインストールします。  
  
****  
```sh:
brew install mongoose
brew install mysql
```  
  
# Perlモジュールをインストール  
  
## local::libをインストール  
  
Perl関係の環境変数を簡単に設定するためにlocal::libをインストールします。  
  
****  
```sh:
~/perl5/bin/cpanm --local-lib=~/perl5/ local::lib
```  
  
## 環境変数を設定  
  
インストールしたlocal::libを使用して環境変数を設定します。  
  
****  
```sh:
eval $(perl -I ~/perl5/lib/perl5/ -Mlocal::lib)
```  
  
## DBI、DBD::mysqlをインストール  
  
cpanmを使ってインストールします。  
  
****  
```sh:
cpanm DBI
cpanm DBD::mysql
```  
  
# MovableTypeを起動  
  
## 設定  
  
MovableTypeのZIPファイルを適当なディレクトリに展開します。  
  
展開した中にある``mt-config.cgi.original``を``mt-config.cgi``という名前に変更して編集します。  
  
## PERLLIB環境変数  
  
mongooseがCGI環境に引き継いでくれる環境変数は限られていて``PERL5LIB``は引き継いでくれませんので、``PERLLIB``を設定します。  
  
****  
```sh:
export PERLLIB=$PERL5LIB:~/perl5/lib/perl5/
```  
  
  
**（書きかけ。。。続きはまた？ :sweat: :sweat: :sweat: ）**  
