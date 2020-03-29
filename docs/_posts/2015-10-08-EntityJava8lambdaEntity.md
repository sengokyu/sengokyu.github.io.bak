---
'created_at: ': '2015-10-08T18:02:34+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Java
      versions: []
    - name: jpa
      versions: []
    - name: glassfish
      versions: []
    - name: EclipseLink
      versions: []
  state: 0
title: "[\u30CF\u30DE\u30EA\u30CD\u30BF]Entity\u30AF\u30E9\u30B9\u306E\u4E2D\u3067\
  Java 8 lambda\u5F0F\u3092\u4F7F\u7528\u3059\u308B\u3068\u3001Entity\u30AF\u30E9\u30B9\
  \u304C\u8AAD\u307F\u8FBC\u307E\u308C\u306A\u3044"
'updated_at: ': '2015-10-08T18:17:42+09:00'

---
# 背景  
  
Entityクラスの中でlambda式を使用していました。  
  
例えばこんな風に。  
****  
```java:
  myCollection.stream().forEach(i -> i.setDate(now));
```  
  
そうすると、そのEntityクラスは読み込まれず、リレーションシップなどで使用していると、次のようなエラーが出ます。  
  
****  
```text:
[class com.example.Hoge] uses a non-entity [class com.example.fuga] as target entity in the relationship attribute [field hoge]
```  
  
# 環境  
  
 - Glassfish 4  
 - Java 8  
  
# 原因  
  
Eclipselinkのバグでしたー。 :sob: :sob: :sob:   
  
[Bug 429992 - EclipseLink silently ignores Entity classes with lambda expressions](https://bugs.eclipse.org/bugs/show_bug.cgi?id=429992)  
  
# 回避策  
  
lambda式を使うのをやめましょー。 :sweat:   
  
  
