---
date: '2016-12-01T12:15:28+09:00'
layout: post
published: true
qiita_article_id: 03939ce47363f2b69803
tags:
- Java
title: Javaのメモリ設定と、jstatの出力結果に関するメモ
updated: '2018-06-28T12:07:21+09:00'

---
# Java起動時のメモリ設定オプション  
  
| オプション | 使用例 | 解説 |  
|:--|:--|:--|  
| -Xms | -Xms512m | 起動時のヒープメモリサイズ。サーバ用途なら、-Xmxと同じ値でよいと思われます。 |  
| -Xmx | -Xmx1024m | ヒープメモリの最大サイズ。 |  
| -Xss | -Xss128m | スタックメモリのサイズ |  
| -XX:NewRatio | -XX:NewRatio=2 | OLD領域 ÷ NEW領域 の最大値。値が2なら、OLD領域:NEW領域は、最大2:1の割合になる |  
| -XX:PermSize | -XX:PermSize=1024m | 起動時のパーマネント領域メモリサイズ。 |  
| -XX:MaxPermSize | -XX:MaxPermSize=1024m | パーマネント領域の最大サイズ。OutOfMemoryが起きるようなら増やす。 |  
| -XX:NewSize | -XX:NewSize=512m | NEW領域の最小サイズ。 |  
| -XX:MaxNewSize | -XX:MaxNewSize=512m | NEW領域の最大サイズ |  
  
# 一般的なメモリ設定  
  
- NEW領域はヒープサイズの3分の1から4分の1にする  
- OLD領域は、NEW領域より大きくする  
- XmsとXmxを同じ値にすると、メモリ割当時の処理が不要になる  
  
# jstatの出力結果  
  
## gcutilオプション使用時  
  
ガーベージコレクションのサマリです。  
  
| 列 | 解説 | 単位 |  
|:-:|:--|:-:|  
| S0  | Survivor領域0(From/To)の使用率  | % |  
| S1  | Survivor領域1(From/To）の使用率   | % |  
| E  | EDEN領域の使用率  | % |  
| O  | OLD領域の使用率  | % |  
| M  | メタ領域の使用率  | % |  
| CCS | 圧縮済クラス領域の使用率 | % |  
| YGC | スカベンジガーベージコレクション（NEW領域のガーベージコレクション）回数 | 回 |  
| YGCT | スカベンジガーベージコレクション（NEW領域のガーベージコレクション）にかかった時間 | ミリ秒 |  
| FGC | フルガーベージコレクションの発生回数 | 回 |  
| FGCT | フルガーベージコレクションにかかった時間 | ミリ秒 |  
| GCT | ガーベージコレクションにかかった時間 | ミリ秒 |  
  
  
# 参考  
  
- [Javaのヒープ・メモリ管理の仕組み (1/2)](http://www.atmarkit.co.jp/ait/articles/0504/02/news005.html)  
- [チューニングに使えるJava性能監視ツール](https://thinkit.co.jp/story/2011/03/25/2054)  
- [jstatマニュアル](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jstat.html)  
