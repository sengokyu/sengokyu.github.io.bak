---
date: '2016-11-11T20:58:17+09:00'
layout: post
published: true
qiita_article_id: 28b068e4ad8745340879
tags:
- Java
- spring
- spring-boot
- spring-data-jpa
title: '[Java][Spring Boot] Customクラスを使用してデータベースアクセスする - NetBeansで始めるSpring Boot (7)'
updated: '2016-11-16T11:26:59+09:00'

---
Spring Bootで[データベースを使う](http://qiita.com/sengoku/items/2424c59f3b4ac94a1cb0)記事を書きました。記事中で軽く触れたCustomクラスを作る方法です。  
  
# 前提  
  
こんなEntityとRepositoryインターフェースがあったとします。  
  
**User.java**  
```java:User.java
@Entity
public class User {
  // いろいろ
}
```  
  
**UserRepository.java**  
```java:UserRepository.java
public interface UserRepository extends CrudRepository<User> {
}
```  
  
# やること  
  
## ステップ1 - Customインターフェースを作成する  
  
名前は必ず__Repositoryインターフェース名 + 'Custom'__にします。  
  
**UserRepositoryCustom.java**  
```java:UserRepositoryCustom.java
public interface UserRepositoryCustom {
  public List<User> findBySpec(String prop);
}
```  
  
## ステップ2 - Customインターフェースの実装を作成する  
  
名前は必ず__Repositoryインターフェース名 + 'Impl'__にします。  
  
**UserRepositoryImpl.java**  
```java:UserRepositoryImpl.java
public class UserRepositoryImpl implements UserRepositoryCustom {
  public List<User> findBySpec(String prop) {
    // いろいろ
  }
}
```  
  
## ステップ3 - RepositoryインターフェースにCustomインターフェースを継承させる  
  
**UserRepository.java**  
```java:UserRepository.java
public interface UserRepository extends
  CrudRepository<User>, UserRepositoryCustom {
}
```  
  
# Customインターフェースの実装について  
  
## `EntityManager`を使う  
  
`@Autowired`で`JpaContext`を取得します。  
  
`JpaContext`から、`EntiyManager`を取得します。  
  
  
**UserRepositoryImpl.java**  
```java:UserRepositoryImpl.java
  @Autowired
  private JpaContext context;

  public List<User> findBySpec(String prop) {
    final EntityManager em = context.GetEntityManagerByManagedType(User.class);

    // いろいろ
  }

```  
  
## Repositoryインターフェースを使う  
  
普通に`@Autowired`すると入ってきます。  
よくできてますね。  
  
**UserRepositoryImpl.java**  
```java:UserRepositoryImpl.java
  @Autowired
  private UserRepository repository;
```  
