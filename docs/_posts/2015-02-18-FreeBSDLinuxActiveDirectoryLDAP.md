---
'created_at: ': '2015-02-18T11:18:20+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: FreeBSD
      versions: []
    - name: ActiveDirectory
      versions: []
  state: 0
title: "FreeBSD/Linux\u306E\u8A8D\u8A3C\u3092Active Directory(LDAP)\u306B\u4EFB\u305B\
  \u308B\u3068\u304D\u3001\u5FD8\u308C\u305A\u306B\u8A2D\u5B9A\u3057\u305F\u3044\u5C5E\
  \u6027\u30EA\u30B9\u30C8"
'updated_at: ': '2015-02-18T11:18:20+09:00'

---
FreeBSD/Linuxの認証をActive Directoryに統合しているひとは多いと思います。  
  
Active Directoryに新規ユーザを追加したのになんでかログインできない、そんなときは必要な属性を忘れているのかもしれません。それをチェックするためのリストです。  
  
 * sAMAccountName ログインIDです。  
 * unixHomeDirectory ホームディレクトリです。/home/usernameとかです。  
 * loginShell シェルです。/bin/bashとかです。  
 * uidNumber ユーザID（数字）です。  
 * gidNumber グループID（数字）です。  
