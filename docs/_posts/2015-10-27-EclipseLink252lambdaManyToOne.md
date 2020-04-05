---
date: '2015-10-27T14:22:36+09:00'
layout: post
published: true
qiita_article_id: c507fa2b51ef676a367f
tags:
- Java
- jpa
- JavaEE
- EclipseLink
title: '[バグ(?)] EclipseLink 2.5.2は、lambdaの中でManyToOneリレーションシップが動かない'
updated: '2015-10-28T09:29:19+09:00'

---
# 前提  
  
こんな感じのOneToMany/ManyToOneなエンティティがあったとします。  
子エンティティ「STREET」は、ひとつの親エンティティ「CITY」を持ちます。  
  
****  
```text:
+-------+ (1)   (n) +--------+
| CITY  |-----------| STREET |
+-------+           +--------+
```  
  
プログラムはこんな感じ（いろいろ略しています）。  
  
**City.java**  
```java:City.java
@Entity
class City {
  @OneToMany(mappedBy="city")
  private List<Street> streetList;
}
```  
  
**Street.java**  
```java:Street.java
@Entity
class Street {
  @ManyToOne
  private City city;

  public City getCity() {
    return city;
  }
}
```  
  
# 問題  
  
子エンティティ「STREET」を取得し、lambdaの中で親エンティティ「CITY」を取得しようとしても、SQLは実行されず取得できません。  
  
**Test.java**  
```java:Test.java
class Test {
  @PersistenceContext
  private EntityManager em;

  public void test {
    em.createQuery("SELECT s FROM Street s", Street.class)
      .getResultList()
      .stream()
      .forEach(x -> System.out.println(x.getCity().toString())); // ← ダメ
  }
```  
  
# 原因  
  
EclipsseLink 2.5.2のバグらしいです。2.6で直っています。  
  
http://stackoverflow.com/questions/33151787/java-stream-with-jpa-one-to-manylazy-relation-not-working  
  
つまりGlassfish 4.1はダメー :scream:   
  
# 回避策  
  
## その1 ``@JoinFetch``をつける  
  
子エンティティに``@JoinFetch``をつけて、あらかじめ親エンティティも取得します。  
  
**Street.java**  
```java:Street.java
@Entity
class Street {
  @ManyToOne
  @JoinFetch             // ←追加
  private City city;

  public City getCity() {
    return city;
  }
}
```  
  
## その2 lambdaを使わない  
  
どうもEclipseLinkとlambdaは相性悪いですね。。。  
  
----  
2015.10.28 追記  
文中しっかりスペルやら間違えていました。  
× lamda  
○ lambda  
× Eclipse 2.5.2  
○ EclipseLink 2.5.2  
  
@laughter さん、 @tag1216 さん、編集リクエストありがとうございました。  
@deaf_tadashi さんコメントありがとうございました。  
  
