---
date: '2016-10-31T18:46:33+09:00'
layout: post
published: true
qiita_article_id: 67552813f521b52bff11
tags:
- Java
- spring-boot
title: '[Java][Spring Boot] ログイン画面を作る - NetBeansで始めるSpring Boot(3)'
updated: '2016-11-07T13:03:46+09:00'

---
[Spring MVCをやめてJersey(JAX-RS)を使おうとしました](http://qiita.com/sengoku/items/891eb6ae0bdb5be1dfa5)が、ドキュメントの整備状況から察するに茨の道のようなので、やっぱりSpring MVCでいくことにします。  
  
  
  
# Spring Bootのセキュリティはどうなっているのか？  
  
まずは[ドキュメント](http://docs.spring.io/spring-boot/docs/1.4.1.RELEASE/reference/htmlsingle/#boot-features-security)を読んでみます。  
  
> クラスパス上にSpring Securityが存在すると、既定ではすべてのHTTPエンドポイントで「BASIC」認証が有効になります。メソッド単位でのセキュリティを追加するには、望んでいる設定に`@EnableGlobalMethodSecurity`を追加します。  
  
`spring-boot-starter-security`への依存を追加するだけで、セキュリティ機能が有効になるのはこういうことですか。  
  
  
> 既定のセキュリティ設定は、`SecurityAutoConfiguration`クラスと、そこからインポートされているクラス達に実装されています（Webセキュリティ用に`SpringBootWebSecurityConfiguration`。認証設定用に`AuthenticationManagerConfiguration`、これはまた非Webアプリケーションでも使われます）。既定のWebアプリケーションセキュリティ設定を完全に無効にするには、`@EnableWebSecurity`をつけたビーンを追加します（これは認証マネージャ設定やActuatorのセキュリティ[^1]は無効にしません）。これをカスタマイズ（例えばフォームベースのログインを追加）するには、通常は外部プロパティを使ったり、`WebSecurityConfigurerAdaptor`型のビーンを使います。認証マネージャ設定も無効にするには、`AuthenticationManager`型のビーンを追加したり、`@Configuration`を付けたクラスにオートワイヤされる`AuthenticationManagerBuilder`を使用してグローバルな`AuthenticationManager`を設定します。  
  
`WebSecurityConfigurerAdaptor`を継承したビーンを追加するのがミソと。  
  
[^1]: なんのとことなのかわからないので、訳さずそのままです。  
  
それでは、[ガイド](https://spring.io/guides/gs/securing-web/)をなぞって、ログイン画面を作っていきます。  
  
  
# pom.xmlを変更  
  
ThymeleafテンプレートとSecurity機能を使えるようにします。  
  
**pom.xml**  
```xml:pom.xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
```  
  
# 設定関連のクラスを作成  
  
## WebSecurityConfigurerAdaptor継承クラス  
  
  
`/login`と`/logout`以外は認証必須にします。  
  
  
**src/main/java/com/example/config/WebSecurityConfig.java**  
```java:src/main/java/com/example/config/WebSecurityConfig.java
package com.example.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()
                .anyRequest().authenticated()
                .and()
                .formLogin().loginPage("/login").permitAll()
                .and()
                .logout().permitAll();
    }
}

```  
  
## WebMvcConfigurerAdaptor継承クラス  
  
URLとビューファイルの設定をします。  
  
**src/main/java/com/example/config/WebMvcConfig.java**  
```java:src/main/java/com/example/config/WebMvcConfig.java
package com.example.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;

@Configuration
public class WebMvcConfig extends WebMvcConfigurerAdapter {
    
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("home");
        registry.addViewController("/login").setViewName("login");
    }
}
```  
  
# ビューの作成  
  
ThymeleafテンプレートエンジンはSAXでビューファイルを読み込んでいるみたいなので、XMLはとして作らないといけないです。  
  
## トップ画面  
  
**src/main/resources/templates/home.html**  
```html:src/main/resources/templates/home.html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" 
      xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
    <head>
        <title>HOME</title>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    </head>
    <body>
        <h1 th:inline="text">Hello [[${#httpServletRequest.remoteUser}]]!</h1>
        <form th:action="@{/logout}" method="post">
            <input type="submit" value="Sign Out"/>
        </form>
    </body>
</html>

```  
  
## ログイン画面  
  
**src/main/resources/templates/login.html**  
```html:src/main/resources/templates/login.html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" 
      xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
    <head>
        <title>LOGIN</title>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    </head>
    <body>
        <div th:if="${param.error}">
            Invalid username and password.
        </div>
        <div th:if="${param.logout}">
            You have been logged out.
        </div>
        <form th:action="@{/login}" method="post">
            <div><label> User Name : <input type="text" name="username"/> </label></div>
            <div><label> Password: <input type="password" name="password"/> </label></div>
            <div><input type="submit" value="Sign In"/></div>
        </form>
    </body>
</html>

```  
  
# ビルドして実行  
  
実行すると、自動作成されたユーザのパスワードが出力されます。  
  
ログインID  
  
![Screenshot from 2016-10-31 18-40-16.png](/assets/images/64676401-8d51-67db-2b56-88a47802a638.png)  
  
`http://localhost:8080/`へアクセスすると、ログイン画面へリダイレクトされました。  
  
![Screenshot from 2016-10-31 18-40-47.png](/assets/images/92e9acf0-e7d9-f73e-de86-562b687d69cd.png)  
  
ログインIDやパスワードが違うと、`login?error`へリダイレクトされました。  
  
![Screenshot from 2016-10-31 18-41-19.png](/assets/images/5d5b387e-43bf-731b-532d-a20752fac603.png)  
  
正しいログインIDでとパスワードを入れるとトップ画面が表示されました。  
  
![Screenshot from 2016-10-31 18-41-45.png](/assets/images/a5af042a-2ad1-58b4-42d4-6c046dcaf7ed.png)  
  
ログアウトすると、`login?logout`へリダイレクトされます。  
  
![Screenshot from 2016-10-31 18-42-03.png](/assets/images/561ca913-1630-0e37-7b41-42a1c1f1abd2.png)  
  
  
# 参考リンク  
  
- [実装サンプル集](https://github.com/spring-projects/spring-boot/tree/v1.4.1.RELEASE/spring-boot-samples/)  
- [Getting Started - Securing a web applicaiton](https://spring.io/guides/gs/securing-web/)  
