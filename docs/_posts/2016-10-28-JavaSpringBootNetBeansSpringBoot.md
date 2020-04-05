---
date: '2016-10-28T16:54:57+09:00'
layout: post
published: true
qiita_article_id: 735cb9639fd8e0686081
tags:
- Java
- NetBeans
- spring-boot
title: '[Java][Spring Boot] NetBeansで始めるSpring Boot'
updated: '2016-10-28T16:54:57+09:00'

---
NetBeansを使用して、Spring Bootアプリケーションを作成していきます。  
  
# 環境  
  
- Java 8  
- NetBeans 8.1  
- NetBeansプラグイン ｰ NB SpringBoot  
  
NetBeansのGradleサポートはいまいちなので、ビルド-ツールはMavenを使用します。  
  
# プロジェクトの作成  
  
MavenカテゴリにあるSpring boot basic projectを選択します。  
  
## 生成されたファイル  
  
このようなファイルたちが生成されました。  
  
```
├── nbactions.xml
├── pom.xml
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── example
    │   │           └── DemoApplication.java
    │   └── resources
    │       └── application.properties
    └── test
        └── java
            └── com
                └── example
                    └── DemoApplicationTests.java

```  
  
## pom.xmlの変更  
  
2016年10月時点では、pom.xmlの内容が古くて読み込まれていないので書き換えます。  
  
**project>parent**  
```xml:project>parent
<version>1.4.0.RELASE</version>
```  
  
**project>properties**  
```xml:project>properties
<java.version>1.7</java.version>
```  
  
↓  
  
**project>parent**  
```xml:project>parent
<version>1.4.1.RELASE</version>
```  
  
**project>properties**  
```xml:project>properties
<java.version>1.8</java.version>
```  
  
POMをReloadします。  
  
![Screenshot from 2016-10-28 16-19-11.png](/assets/images/e8af35e5-c2a3-9030-088a-d81a77294d80.png)  
  
読み込まれました。  
  
## 依存性の変更  
  
`spring-boot-starter`となっているところを`spring-boot-starter-web`へ変更します。  
  
# ビルドして実行  
  
おもむろにビルドして実行してみます。  
  
ブラウザを起動して http://localhost:8080/ へアクセスします。  
  
![Screenshot from 2016-10-28 16-48-58.png](/assets/images/1de7eed4-f929-71a9-5a3c-ed525e51c639.png)  
  
# Hello Controllerの作成  
  
[Guide](https://spring.io/guides/gs/spring-boot/)に従って、Hello Controllerを作成してみます。  
  
**HelloController.java**  
```java:HelloController.java
package com.example.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @RequestMapping("/")
    public String index() {
        return "Greetings from Spring Boot!";
    }
}

```  
  
## 実行  
  
ビルドして実行します。  
もし前のMavenが動いていたら、先に止めておきます。  
  
![Screenshot from 2016-10-28 16-43-54.png](/assets/images/609699ee-90c6-603c-5b2d-b7332f01913a.png)  
  
こんなん表示されました。  
  
  
  
