---
date: '2016-11-22T10:40:06+09:00'
layout: post
published: true
qiita_article_id: 51ef99ec73b6593d9860
tags:
- MySQL
- jpa
- spring-boot
title: '[Java][Spring Boot][MySQL] テーブル名/列名等の大文字小文字を区別する方法 - NetBeansで始めるSpring Boot
  (9)'
updated: '2016-11-22T10:40:06+09:00'

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
  
