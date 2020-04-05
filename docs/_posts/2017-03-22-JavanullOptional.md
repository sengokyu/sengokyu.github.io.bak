---
date: '2017-03-22T22:11:32+09:00'
layout: post
published: true
qiita_article_id: 333ff38c670c758633e4
tags:
- Java
- null安全
title: '[Java] nullを返すメソッドの戻り値はOptional<>にすると幸せかもしれない'
updated: '2017-03-22T22:11:32+09:00'

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
