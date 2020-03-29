---
'created_at: ': '2016-11-22T10:40:06+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: MySQL
      versions: []
    - name: jpa
      versions: []
    - name: spring-boot
      versions: []
  state: 0
title: "[Java][Spring Boot][MySQL] \u30C6\u30FC\u30D6\u30EB\u540D/\u5217\u540D\u7B49\
  \u306E\u5927\u6587\u5B57\u5C0F\u6587\u5B57\u3092\u533A\u5225\u3059\u308B\u65B9\u6CD5\
  \ - NetBeans\u3067\u59CB\u3081\u308BSpring Boot (9)"
'updated_at: ': '2016-11-22T10:40:06+09:00'

---
# 環境  
  
- Spring Boot 1.4.2  
- Java 8  
  
  
# 概要  
  
例えば、以下のようにエンティティのテーブル名を大文字にしていても、データベースには小文字でアクセスするようになっています。  
  
****  
```java:
@Entity
@Table("MY_ENTITY")
class MyEntity {
  // ...
}
```  
  
これを、大文字でアクセスするようにする方法です。  
  
# 詳細  
  
Spring Bootのデフォルトでは、[物理名マッピングするためのクラスが設定されています](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#howto-configure-jpa-properties)。  
  
物理名マッピングは、[SpringPhysicalNamingStrategyクラス](https://github.com/spring-projects/spring-boot/blob/master/spring-boot/src/main/java/org/springframework/boot/orm/jpa/hibernate/SpringPhysicalNamingStrategy.java)で行われています。  
  
上記クラスの中ですべて小文字変換しているので、大文字小文字を区別するMySQLでは困ってしまいます。  
  
# 解決策  
  
apprication.ymlまたは同propertiesの中で、物理名マッピングするクラスをHibernateオリジナルのものに変更します。  
  
**application.yml**  
```yaml:application.yml
spring:
     jpa:
        hibernate:
            naming:
                physical-strategy: org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
```  
  
# 真の解決策  
  
テーブル名/列名は小文字にするのが安心安全です。 :smile:  
  
