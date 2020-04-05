---
date: '2015-02-18T11:18:20+09:00'
layout: post
published: true
qiita_article_id: bd4bbc3873444f12a7d1
tags:
- FreeBSD
- ActiveDirectory
title: FreeBSD/Linuxの認証をActive Directory(LDAP)に任せるとき、忘れずに設定したい属性リスト
updated: '2015-02-18T11:18:20+09:00'

---
FreeBSD/Linuxの認証をActive Directoryに統合しているひとは多いと思います。  
  
Active Directoryに新規ユーザを追加したのになんでかログインできない、そんなときは必要な属性を忘れているのかもしれません。それをチェックするためのリストです。  
  
 * sAMAccountName ログインIDです。  
 * unixHomeDirectory ホームディレクトリです。/home/usernameとかです。  
 * loginShell シェルです。/bin/bashとかです。  
 * uidNumber ユーザID（数字）です。  
 * gidNumber グループID（数字）です。  
