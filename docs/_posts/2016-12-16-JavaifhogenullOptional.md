---
'created_at: ': '2016-12-16T18:34:13+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Java
      versions: []
    - name: "null\u5B89\u5168"
      versions: []
  state: 0
title: "[Java] if(hoge != null) {...}\u306E\u4EE3\u308F\u308A\u306BOptional\u3092\u4F7F\
  \u3046"
'updated_at: ': '2016-12-16T18:34:13+09:00'

---
いまいちOptionalの使いドコロがわかっていなかったのですが、こういうことだったのかなぁと理解しました。  
  
# 変数`hoge`が`null`でないときのみ、`hoge`のメソッドを実行したい  
  
## 従来のやりかた  
  
****  
```java:
if (hoge != null) {
    hoge.fuga();
}
```  
  
## `Optional`を使ったやりかた  
  
****  
```java:
Optional
    .ofNullable(hoge)
    .ifPresent(o -> o.fuga());
```  
  
どっちのほうが見やすいかな？ :smile:   
  
# 変数`hoge`が`null`でないときは、`hoge`のメソッド実行結果を返す  
  
## 従来のやりかた  
  
****  
```java:
fugo = hoge != null ? hoge.fugo() : null;
```  
  
## `Optional`を使ったやりかた  
  
****  
```java:
fugo = Optional
    .ofNullable(hoge)
    .map(o -> o.fugo())  // Optional<?>が返ってくるので、
    .orElse(null);       // orElseで値そのものか、デフォルト値（ここではnull）を返すようにする
```  
  
  
これくらいだと、3項演算子のほうが見やすいかな？ :smile:   
  
# 変数`hoge`が`null`でないときは、`hoge`のメソッドを呼び、メソッドの結果が`null`でないときは、さらにメソッドを呼ぶ  
  
何書いてあるかわかんないですね。コード見てください。  
  
## 従来のやりかた  
  
****  
```java:
instant = null;

if (hoge != null) {
    date = hoge.getDate();

    if (date != null) {
        instant = date.toInstant();
    }
}
```  
  
あるいは、こんなんどうでしょう？  
  
****  
```java:
instant = hoge != null
    ? (hoge.getDate() != null
        ? hoge.getDate().toInstant()
        : null) 
    : null;
```  
  
## `Optional`を使ったやりかた  
  
```java
instant = Optional
  .ofNullable(hoge)
  .map(o -> o.getDate())     
  .map(d -> d.toInstant())   
  .orElse(null);
```  
  
どっちのほうが見やすいかな？ :smile:   
  
  
  
