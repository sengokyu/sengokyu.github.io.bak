---
'created_at: ': '2016-12-08T22:32:21+09:00'
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
    - name: Hibernate
      versions: []
    - name: jersey
      versions: []
    - name: spring-boot
      versions: []
  state: 0
title: "[Java] Hibernate\u3068MultipleBagException\u3068LazyInitializationException"
'updated_at: ': '2016-12-09T10:49:31+09:00'

---
# 環境  
  
- Spring Boot 1.4.2  
- Hibernate 5.1.1(?)  
  
# 前提  
  
このようなエンティティたちがありました。  
  
```
+------------+
|Grand Parent|
+-+----------+
  |1
  |
  |*
+-+----+
|Parent|
+-+----+
  |1
  |
  |*
+-+---+
|Child|
+-----+
```  
  
それぞれJavaソースはこのような感じになっています。  
  
**GrandParent.java**  
```java:GrandParent.java
public class GrandParent {
    // 略
    @OneToMany(mappedBy="grandParent")
    private Collection<Parent> parentCollection;
}
```  
  
**Parent.java**  
```java:Parent.java
public class Parent {
    // 略
    @ManyToOne
    private GrandParent grandParent;

    @OneToMany(mappedBy="parent")
    private Collection<Child> childCollection;
}
```  
  
**Parent.java**  
```java:Parent.java
public class Parent {
    // 略
    @ManyToOne
    private Parent parent;
}
```  
  
# 発生した問題  
  
## LazyInitializationException  
  
Jerseyリソースから`Parent`を取得し、`childCollection`にアクセスすると、`LazyInitializationException`が発生します。  
  
Hibernate(?)のセッション外なのが原因だそうです。  
  
なんでだよ！detachしてないよ！  
  
FETCH戦略をLAZYからEAGERに変えて、先にロードしてしまえばよいみたいです。  
  
具体的にはこのように変えます。  
  
```
    // @OneToMany(mappedBy="parent")
    @OneToMany(mappedBy="parent", fetch=FetchType.EAGER)
```  
  
やれやれ、これで一安心かと思いきや……。  
  
## MultipleBagException  
  
実行時ではなく初期化時に`MultipleBagException`が発生しました。  
  
複数個Collectionがあったり、親も子もあったりすると、EAGER戦略を通してくれないようです。  
  
`org.hibernate.annotations.Fetch`をつければ解決するとのこと？  
  
ドキュメント → http://docs.jboss.org/hibernate/orm/5.2/userguide/html_single/Hibernate_User_Guide.html#fetching-fetch-annotation  
  
```
    // @OneToMany(mappedBy="parent")
    @OneToMany(mappedBy="parent", fetch=FetchType.EAGER)
    @Fetch(FetchMode.SUBSELECT)
```  
  
これで`MultipleBagException`は出なくなりました。  
  
やれやれ……！？  
  
## 再びLazyInitializationException  
  
まただよ！  
  
さらにぐぐると、EAGERはだめなのでLazyCollectionを使えとのこと。  
  
ドキュメント → http://docs.jboss.org/hibernate/orm/5.2/userguide/html_single/Hibernate_User_Guide.html#annotations-hibernate-lazycollection  
  
```
    @OneToMany(mappedBy="parent")
    // @OneToMany(mappedBy="parent", fetch=FetchType.EAGER)
    // @Fetch(FetchMode.SUBSELECT)
    @LazyCollection(LazyCollectionOption.FALSE)
```  
  
  
## まだまだLazyInitializationException  
  
まだだめでした！  
  
ドキュメントをよく読むと  
  
> The TRUE and FALSE values are deprecated since you should be using the JPA FetchType attribute of the @ElementCollection, @OneToMany, or @ManyToMany collection  
  
`@LazyCollection(LazyCollectionOption.FALSE)`は、単にEAGER戦略指定しているのと一緒なのですね。MultipleBagException出ませんけれど。  
  
  
# 解決策  
  
探求中です。。。  :sob:  
  
  
  
  
