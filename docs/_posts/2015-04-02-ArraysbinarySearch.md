---
date: '2015-04-02T14:35:35+09:00'
layout: post
published: true
qiita_article_id: bbcacdbd082cffd5b886
tags:
- Java
title: Arrays#binarySearchを使うときに注意したいこと
updated: '2015-04-02T14:35:35+09:00'

---
Java Collections Frameworkにある[Arrays#binarySearch](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html) はこんな感じに使います。  
  
```
int result = Arrays.binarySearch(検索対象配列, 検索キー);
```  
  
resultに見つかった要素のインデックスが入ります。  
  
このメソッドを使う際に注意しておきたいことです。  
  
# (1) 検索対象配列をソートしておく  
  
二分探索なので、ソートしておかないと結果がどうなるかはわかりません。  
たまたま期待した結果になるかもしれませんし、ならないかもしれません。  
  
# (2) 見つかったかどうかだけ知りたいときは``>=0``で比較する  
  
``Arrays#binarySearch`` は、検索キーが見つかった配列内要素のインデックスが返ります。  
  
検索キーが見つからないときは、-(配列のサイズ+1)～-1の範囲の値が返ります。  
  
# (3) 検索対象配列に重複する値があると結果は不定  
  
先頭要素のインデックスが返ってきて欲しい気もしますが、二分探索なのでどうなるかはわかりません。  
