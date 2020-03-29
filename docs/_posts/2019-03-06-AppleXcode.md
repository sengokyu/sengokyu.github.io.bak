---
'created_at: ': '2019-03-06T13:54:53+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Xcode
      versions: []
    - name: apple
      versions: []
  state: 0
title: "[Apple] Xcode\u521D\u5FC3\u8005\u3067\u3059\u3002\u30D3\u30EB\u30C9\u6210\u529F\
  \u3059\u308B\u306E\u306B\u30B3\u30F3\u30D1\u30A4\u30EB\u30A8\u30E9\u30FC\u304C\u6D88\
  \u3048\u307E\u305B\u3093"
'updated_at: ': '2019-03-06T13:54:53+09:00'

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
  
  
  
