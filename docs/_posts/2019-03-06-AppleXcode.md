---
date: '2019-03-06T13:54:53+09:00'
layout: post
published: true
qiita_article_id: 9e43a79bda9d94fbed95
tags:
- Xcode
- apple
title: '[Apple] Xcode初心者です。ビルド成功するのにコンパイルエラーが消えません'
updated: '2019-03-06T13:54:53+09:00'

---
# 環境  
  
* Xcode 10.1  
* macOS Mojave 10.14.3  
  
  
# 現象  
  
Xcodeでアプリを作りました。  
ビルド成功し、実行すれば動きます。  
それなのに、エディタ上のコンパイルエラーが消えません。  
  
# 解決策  
  
`~/Library/Developer/Xcode/DerivedData` の下にプロジェクト毎のフォルダがあるので、そのフォルダを消します。  
  
# 原因  
  
キャッシュ？  
でしょうか？  
  
# 参考リンク  
  
昔からある不具合っぽいですね。  
  
Xcode shows many errors but program compiles and runs fine (in both simulator and device)  
https://stackoverflow.com/questions/12023484/xcode-shows-many-errors-but-program-compiles-and-runs-fine-in-both-simulator-an/20523935  
  
Xcode 4.5.2 errors are found but Build is marked as Succeeded  
https://stackoverflow.com/questions/13954229/xcode-4-5-2-errors-are-found-but-build-is-marked-as-succeeded  
  
  
  
