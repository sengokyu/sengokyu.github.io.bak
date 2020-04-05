---
date: '2016-10-28T18:03:33+09:00'
layout: post
published: true
qiita_article_id: 891eb6ae0bdb5be1dfa5
tags:
- Java
- JAX-RS
- jersey
- spring-boot
title: '[Java][Spring Boot][JAX-RS] Spring BootでJAX-RSを使う - NetBeansで始めるSpring Boot
  (2)'
updated: '2016-10-28T18:03:33+09:00'

---
[Hello World!を作成しました](http://qiita.com/sengoku/items/735cb9639fd8e0686081)。  
  
Spring Bootは、Spring MVCではなくJAX-RS実装であるところの[Jerseyも使える](http://docs.spring.io/spring-boot/docs/1.4.1.RELEASE/reference/htmlsingle/#boot-features-jersey)そうなので、慣れているこちらを使ってみることにします。  
  
# pom.xmlを変更  
  
依存性にある`spring-boot-starter-web`を`spring-boot-starter-jersey`へ書き換えます。  
  
# Hello Controllerを削除  
  
前回作成したHello Controllerは、Spring MVCのファイルだったみたいです。  
よーしらんけど。  
  
使わないので消してしまいます。  
  
# Jerseryを使う  
  
## リソースファイルの作成  
  
`@Component`アノテーションが必要みたいです。  
  
**HelloResource.java**  
```java:HelloResource.java
package com.example.resource;

import javax.ws.rs.GET;
import javax.ws.rs.Path;
import org.springframework.stereotype.Component;

@Component
@Path("/")
public class HelloResource {

    @GET
    public String index() {
        return "Hello Spring Jersey!";
    }
}
```  
  
## アプリケーションファイルの作成  
  
JAX-RSリソースの場所を教えるためのクラスを作成します。  
  
**JerseyConfig.java**  
```java:JerseyConfig.java
package com.example;

import javax.ws.rs.ApplicationPath;
import org.glassfish.jersey.server.ResourceConfig;
import org.springframework.stereotype.Component;

@Component
@ApplicationPath("/rs")
public class JerseyConfig extends ResourceConfig {

    public JerseyConfig() {
        packages("com.example.resource");
    }
    
}
```  
  
`@ApplicationPath`アノテーションで、パスを設定しています。  
  
# ビルドして実行  
  
ビルドして実行したら、 http://localhost:8080/rs/ へアクセスしてみます。  
  
![Screenshot from 2016-10-28 18-02-48.png](/assets/images/a1abb02b-335a-679d-2096-c20ac4d7d210.png)  
  
こんなん表示されました。  
