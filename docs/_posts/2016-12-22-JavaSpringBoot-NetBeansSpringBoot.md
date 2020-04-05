---
date: '2016-12-22T17:36:34+09:00'
layout: post
published: true
qiita_article_id: 29e2960079adb5dec196
tags:
- Java
- NetBeans
- spring
- spring-boot
title: '[Java][Spring Boot] 実行時のプロファイルを指定する - NetBeansで始めるSpring Boot'
updated: '2016-12-22T17:36:34+09:00'

---
NetBeansからプロジェクトを実行するとき、Spring Bootのプロファイルを指定する方法です。  
  
プロジェクトを右クリック ＞ プロパティ ＞ ツリーからＲｕｎを選ぶ  
  
必要があれば、[Configuration]ドロップダウンからMavenのプロファイルを選びます。  
  
[VM Options]にJavaへの引数を入力します。  
  
```
-Dspring.profiles.active=development
```  
  
![Screenshot from 2016-12-22 17-32-16.png](/assets/images/20f5dc23-dff5-0400-79f7-84f5c5125f31.png)  
  
  
# 環境  
  
- Java 8  
- Spring Boot 1.2.7  
- NetBeans 8.1  
