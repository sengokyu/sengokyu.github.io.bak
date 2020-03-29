---
'created_at: ': '2015-04-02T14:35:35+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Java
      versions: []
  state: 0
title: "Arrays#binarySearch\u3092\u4F7F\u3046\u3068\u304D\u306B\u6CE8\u610F\u3057\u305F\
  \u3044\u3053\u3068"
'updated_at: ': '2015-04-02T14:35:35+09:00'

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
