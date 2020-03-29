---
'created_at: ': '2016-08-16T09:50:55+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Emacs
      versions: []
  state: 0
title: "[IDE][NetBeans][Visual Studio] \u3069\u3053\u3067\u3082Emacs\u30AD\u30FC\u30D0\
  \u30A4\u30F3\u30C9\u3067\u4F7F\u3044\u305F\u3044"
'updated_at: ': '2016-08-16T09:50:55+09:00'

---
いろいろな開発環境でEmacsキーバインドがどの程度使えるか/使うためのメモです。  
  
| キーバインド | NetBeans | Visual Studio 2010 | Visual Studio 2013 | Visual Studio 2015 |  
|:-:|:-:|:-:|:-:|:-:|:-:|  
|カーソル操作<br>C-n<br>C-p<br>C-f<br>C-b | OK | OK | OK | OK |  
|カーソル操作<br>C-a<br>C-e | OK | OK | OK | OK |  
|コピペ関連<br>C-w<br>M-w<br>C-k<br>C-y | OK [^nbkr] | OK [^vskr] | OK [^vskr] | OK [^vskr] |  
  
  
[^nbkr]: 1回分のみ  
[^vskr]: テキストエディタのみ  
