---
date: '2017-01-18T10:53:32+09:00'
layout: post
published: true
qiita_article_id: 26e63031d9473e8f169b
tags:
- Java
- spring
title: '[Java][Spring] Spring Securityのアノテーションを付けたら、Type MismatchでAutowireできないときの対処方法'
updated: '2017-01-18T10:53:32+09:00'

---
# 背景  
  
Spring Securityのメソッドアノテーションでアクセス制御したいと思いました。  
  
既存のクラスにアノテーションを付けました。  
  
**MyService.java**  
```java:MyService.java
@Component
public class MyService {

    @PreAuthorize("hasRole('ROLE_ADMIN')")
    public void someAdminAction() {
        /* ... */
    }
}
```  
  
アノテーションを有効にするためにコンフィグレーションクラスを作りました。  
  
**MethodSecurityConfiguration.java**  
```java:MethodSecurityConfiguration.java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class MethodSecurityConfiguration {

}
```  
  
`MyService`はコントローラにインジェクションしていました。  
  
**MyController.java**  
```java:MyController.java
@RestController
public class MyController {

    @Autowired
    private MyService myService;

    /* ... */
}
```  
  
# 起きたこと  
  
MyControllerにMyServiceをインジェクションできない！  
  
なんかType Mismatchとかいってる！！  
  
# 原因  
  
アノテーションを付けたクラスはAOPしてプロキシになるので、型が変わっている（？）みたいです。  
  
  
# 解決策  
  
`proxyTargetClass = true`というオプションを追加しました。  
  
**MethodSecurityConfiguration.java**  
```java:MethodSecurityConfiguration.java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true, proxyTargetClass = true)
public class MethodSecurityConfiguration {

}
```  
  
  
  
