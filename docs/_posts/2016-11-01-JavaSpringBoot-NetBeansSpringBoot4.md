---
date: '2016-11-01T11:14:05+09:00'
layout: post
published: true
qiita_article_id: 3ccd4be817363590ae4c
tags:
- Java
- NetBeans
- spring-boot
title: '[Java][Spring Boot] 自動デプロイする - NetBeansで始めるSpring Boot (4)'
updated: '2016-11-01T11:14:05+09:00'

---
NetBeansで始めるSpring Bootと題して、記事を３つ書きました。  
  
1. [NetBeansで始めるSpring Boot](http://qiita.com/sengoku/items/735cb9639fd8e0686081)  
1. [Spring BootでJAX-RSを使う](http://qiita.com/sengoku/items/891eb6ae0bdb5be1dfa5)  
1. [ログイン画面を作る](http://qiita.com/sengoku/items/67552813f521b52bff11)  
  
「NetBeansで〜」と題しながら、自動デプロイが動かず不便でした。  
  
アプリケーションサーバとしてTomcatを動かし、自動デプロイさせるようにします。  
  
  
# アプリケーションクラスを変更  
  
アプリケーションクラスを`SpringBootServletInitializer`から継承させるようにします。  
  
**src/main/java/com/example/DemoApplication.java**  
```java:src/main/java/com/example/DemoApplication.java
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.support.SpringBootServletInitializer;

@SpringBootApplication
public class DemoApplication extends SpringBootServletInitializer {

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        return builder.sources(DemoApplication.class);
    }

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```  
  
`main`メソッドは不要な気もするのですが、ないとビルド通らないです。  
  
  
# pom.xmlを変更  
  
## packagingを`war`へ  
  
`packaging`を`jar`から`war`へ変更します。  
  
**pom.xml**  
```xml:pom.xml
    <packaging>jar</packaging>
```  
  
↓  
  
**pom.xml**  
```xml:pom.xml
    <packaging>war</packaging>
```  
  
## 組み込みTomcatを依存性から外す  
  
`scope`を`provided`にします。  
  
**pom.xml**  
```xml:pom.xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
            <scope>provided</scope>
        </dependency>
```  
  
# プロジェクトのプロパティを変更  
  
プロジェクトを右クリック > [Properties]メニュー > [Run]ツリーとすすみ、ServerとしてTomcatを選択します。  
  
![Screenshot from 2016-11-01 09-24-22.png](/assets/images/0df4ba38-198d-70ed-30b0-8ffd2c92e8c1.png)  
  
[Actions]ツリーへすすみ、ActionsとしてRun project、Debug project、Profile projectをそれぞれ選択し、[Remove/Reset]ボタンをクリックします。  
  
![Screenshot from 2016-11-01 09-28-01.png](/assets/images/e8a8b9cc-335a-0bf1-f0b1-2830e0ec4d42.png)  
  
  
# ビルドして実行……その前に  
  
Tomcat 8.5以降を使用する場合は、NetBeans 8.2以降じゃないと、起動/停止に失敗します。  
  
# ビルドして実行  
  
ちゃんとブラウザが起動します。  
  
テンプレートを変更して保存すると、自動デプロイされて反映されます。  
  
  
