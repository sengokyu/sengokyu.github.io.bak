---
'created_at: ': '2016-12-22T17:36:34+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Java
      versions: []
    - name: NetBeans
      versions: []
    - name: spring
      versions: []
    - name: spring-boot
      versions: []
  state: 0
title: "[Java][Spring Boot] \u5B9F\u884C\u6642\u306E\u30D7\u30ED\u30D5\u30A1\u30A4\
  \u30EB\u3092\u6307\u5B9A\u3059\u308B - NetBeans\u3067\u59CB\u3081\u308BSpring Boot"
'updated_at: ': '2016-12-22T17:36:34+09:00'

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
