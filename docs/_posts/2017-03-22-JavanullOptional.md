---
'created_at: ': '2017-03-22T22:11:32+09:00'
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
title: "[Java] null\u3092\u8FD4\u3059\u30E1\u30BD\u30C3\u30C9\u306E\u623B\u308A\u5024\
  \u306FOptional<>\u306B\u3059\u308B\u3068\u5E78\u305B\u304B\u3082\u3057\u308C\u306A\
  \u3044"
'updated_at: ': '2017-03-22T22:11:32+09:00'

---
# Optional<>前夜  
  
例えば、こんなメソッドを作ったとします。  
  
**Factory.Java**  
```java:Factory.Java
public class Factory {
    /**
     * MyObjの新規インスタンスを作成して返す。<br>
     * 作成失敗したときはnullを返す。
     *
     * @return
     */
    public MyObj createMyObj() {
      // ...
    }
}
```  
  
このメソッドを使う側は、こんな感じのコードになるでしょう。  
  
****  
```java:
Factory factory;
// ...
MyObj myObj = factory.createMyObj();

if (myObj != null) {
    // 成功時の処理
}
```  
  
# Optional<>後夜  
  
Optional<>を返すようにしてみました。  
  
**Factory.Java**  
```java:Factory.Java
public class Factory {
    /**
     * MyObjの新規インスタンスを作成して返す。<br>
     * 作成失敗したときはnullを返す。
     *
     * @return
     */
    public Optional<MyObj> createMyObj() {
      // ...
    }
}
```  
  
使う側はこんな感じになります。  
  
****  
```java:
Optional<MyObj> myObj = factory.createMyObj();

myObj.ifPresent(o -> {
    // 成功時の処理
});
```  
