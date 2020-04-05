---
date: '2016-11-07T17:53:12+09:00'
layout: post
published: true
qiita_article_id: eda42338db43362bd69c
tags:
- Java
- spring
- spring-boot
title: '[Java][Spring Boot] 依存性注入(Dependency Injection)の使い方 - NetBeansで始めるSpring Boot
  (5)'
updated: '2016-11-08T13:00:40+09:00'

---
Spring BootのDIは、どのように使えばよいのでしょうか。  
  
  
はじめに、[Spring Bootのドキュメント](http://docs.spring.io/spring-boot/docs/1.4.1.RELEASE/reference/htmlsingle/#using-boot-spring-beans-and-dependency-injection)を読んでみます。  
  
> ビーンや注入される依存性の定義には、Springフレームワークの標準技術を随意に使用できます。簡単にいうと、ビーンを探索するために`@ComponentScan`を使っているものを探します。これは`@Autowired`コンストラクタインジェクションとの組み合わせて動きます。  
>   
> 前述で示唆したようにコードを構成（アプリケーションクラスをパッケージのルートに置く）していれば、`@ComponentScan`を引数無しで追加できます。すべてのコンポーネント(`@Component`、`@Service`、`@Repository`、`@Controller`等)は、自動的にSpringビーンとして登録されます。  
>  
> `@Service`ビーンの例です。コンストラクタで`RiskAssessor`ビーンへの依存性を定義しています。  
  
****  
```java:
package com.example.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class DatabaseAccountService implements AccountService {

    private final RiskAssessor riskAssessor;

    @Autowired
    public DatabaseAccountService(RiskAssessor riskAssessor) {
        this.riskAssessor = riskAssessor;
    }

    // ...

}
```  
  
ビーンを欲しいところに`@AutoWired`を付けておけば、うまいことやってくれるみたいです。  
  
# お試し  
  
## 依存される側  
  
コンポーネントを作成します。  
  
**src/main/java/com/example/component/HelloComponent.java**  
```java:src/main/java/com/example/component/HelloComponent.java
package com.example.component;

import org.springframework.stereotype.Component;

@Component
public class HelloComponent {

    public String greet(String name) {
        return "Hello " + name;
    }

}
```  
  
## 依存する側  
  
`/greet?name=hogehoge`というURLへのアクセスで、`HelloComponent`を呼び出してみます。  
  
**src/main/java/com/example/controller/HelloController.java**  
```java:src/main/java/com/example/controller/HelloController.java
package com.example.controller;

import com.example.component.HelloComponent;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    private final HelloComponent helloComponent;

    @Autowired
    public HelloController(HelloComponent helloComponent) {
        this.helloComponent = helloComponent;
    }
    
    @RequestMapping("/greet")
    public String greet(@RequestParam(value = "name", required = true) String name) {
        return helloComponent.greet(name);
    }

}
```  
  
## 実行結果  
  
こんなんできました。  
  
![Screenshot from 2016-11-07 17-09-52.png](/assets/images/1c75aa85-0430-e506-ad27-264ee41ba5ba.png)  
  
  
# スコープはどうなっているのか？  
  
Java EEのCDIでは、アプリケーションスコープ（シングルトン）、リクエストスコープ、セッションスコープ、会話スコープ、依存スコープを定義できました。  
  
[ドキュメント](http://docs.spring.io/spring/docs/4.3.3.RELEASE/spring-framework-reference/html/beans.html#beans-factory-scopes)によりますと、  
  
- singleton  
- prototype<br>必要とされる度にインスタンスが作成されます。  
- request  
- session  
- globalSesion  
- application  
- websocket  
  
があるみたいです。いっぱいですね。デフォルトではsingletonになるそうです。  
  
さらには自前のスコープも定義できるとか。  
  
下5つは、Webアプリケーションコンテキストでないと使用できないです。  
  
## 使い方  
  
| スコープ | 使い方 |  
|:-:|:--|  
| singleton   | デフォルトなので、何もしなくてOK  |  
| prototype  | XMLで定義? `@Scope("prototype")`をつける？   |  
| request  | `@RequestScope`アノテーションをつける  |  
| globalSession | XMLで定義?  |  
| application | `@ApplicationScope`アノテーションをつける  |  
| websocket | XMLで定義? |  
  
  
## スコープがかぶったらどうなる？  
  
singletonにprototypeを注入したらどうなるのか？  
  
TODO: :pencil:   
  
  
# 注入のしかた  
  
次の場所に注入できます。  
  
- コンストラクタ  
- セッター  
- プロパティ  
  
  
