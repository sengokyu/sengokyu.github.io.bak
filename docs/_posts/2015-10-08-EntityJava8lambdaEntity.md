---
date: '2015-10-08T18:02:34+09:00'
layout: post
published: true
qiita_article_id: 42b7e9f3278115d8d4ad
tags:
- Java
- jpa
- glassfish
- EclipseLink
title: '[ハマリネタ]Entityクラスの中でJava 8 lambda式を使用すると、Entityクラスが読み込まれない'
updated: '2015-10-08T18:17:42+09:00'

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
  
  
