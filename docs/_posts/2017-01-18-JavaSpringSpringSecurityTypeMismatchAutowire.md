---
'created_at: ': '2017-01-18T10:53:32+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Java
      versions: []
    - name: spring
      versions: []
  state: 0
title: "[Java][Spring] Spring Security\u306E\u30A2\u30CE\u30C6\u30FC\u30B7\u30E7\u30F3\
  \u3092\u4ED8\u3051\u305F\u3089\u3001Type Mismatch\u3067Autowire\u3067\u304D\u306A\
  \u3044\u3068\u304D\u306E\u5BFE\u51E6\u65B9\u6CD5"
'updated_at: ': '2017-01-18T10:53:32+09:00'

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
  
  
  
