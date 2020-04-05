---
date: '2016-11-10T14:36:45+09:00'
layout: post
published: true
qiita_article_id: 2424c59f3b4ac94a1cb0
tags:
- Java
- spring
- jpa
- spring-boot
- spring-data-jpa
title: '[Java][Spring Boot][JPA] データベースを使う - NetBeansで始めるSpring Boot (6)'
updated: '2016-11-17T19:57:54+09:00'

---
# 概要  
  
Spring Bootは、データベースアクセスにSpring Dataを使用します。  
Spring Dataは、JPAやMongoDBなどのデータストアへのアクセスを抽象化する層です。  
  
自分はJPAを使います。  
  
# 作らなくちゃいけないもの  
  
## Entityクラス  
  
データベースのテーブルへ対応するクラスです。  
  
すでにデータベースが用意されているのであれば、NetBeansの機能を使って生成するのが簡単です。  
  
[File]>[New File]メニューから、Persistence > Entity Classes from Databaseとたどれば、データベースのテーブルからEntityクラスを生成できます。  
  
## Repositoryインターフェース  
  
Entityを操作するインターフェースです。`org.springframework.data.repository.Repository<T, ID extends Serializable>`の派生インターフェースにします。  
  
派生インターフェースとして、あらかじめ次のものが用意されています。  
  
| インターフェース | 解説 |  
|:--|:--|  
| CrudRepository  | 一般的なCread/Read/Update/Delete操作が定義されています  |  
| PagingAndSortingRepository  | ソートとページング付きの全検索が定義されています。CrudRepositoryを継承しています。  |  
  
  
  
# 使い方  
  
Repositoryインターフェースに対して`@Autowired`等で依存しておくと、Springが自動的にプロキシインスタンスを作成してくれます。  
  
****  
```java:
@Component
public class SomeClass {
  // ...
  @Autowired
  private SomeRepository someRepository;
  // ...
}
```  
  
あとは定義されているメソッドを呼び出せばよいです。  
  
# CRUD以外の操作をしたい  
  
`CrudRepository`にあらかじめ用意されているCRUD以外の操作をしたいときのやり方です。  
  
## 方法1 - 命名規約にそったメソッドを実装  
  
以下の命名規約にそったメソッドを実装すると、メソッド名に応じて振る舞いが用意されます。  
  
- `find...By`  
- `read...By`  
- `get...By`  
- `query...By`  
- `stream...By` Java 8のStream<T>を返します。  
- `count...By`  
- `exists...By`  
- `delete...By`  
- `remove...By`  
  
最初に出てくる`By`より後ろが検索式になります。  
  
`...`の副式部分には`Distinct`や、リレーション先のEntity名等が入ります。  
  
  
### 検索式いろいろ  
  
あらかじめいろいろなものが用意されています。  
  
**2016/11/11追記** [【Spring Data JPA】自動実装されるメソッドの命名ルール](http://qiita.com/sndr/items/af7d12be264c2cc4b252) に解説がありました。  
  
| 検索式 | 例 |  
|:--|:--|  
| 2項目をAndで結ぶ | `findByFirstnameAndLastname(String lastname)`  |  
| 2項目をOrで結ぶ  | `findByFirstnameOrLastname(String firstname, String lastname)` |  
| 2項目の間 | `findByCreateDateBetween(Date since, Date to)` |  
| After/GreaterThan ||  
| GreaterThanEqual ||  
| Before/LessThan ||  
| LessThanEqual ||  
| IsNull| | |  
| IsNotNull | |  
| NotIn | |  
| In | |  
| StartingWith/EndingWith/Containing | |  
| NotContaining | |  
| Like | |  
| NotLike | |  
| 大文字小文字を無視 | `findByFirstnameIgnoreCase(String firstname)` |  
| ソート  | `findAllOrderByFirstnameAsc()` `findAllOrderByFirstnameDesc()` |  
  
### 副式`...`に入るもの  
  
| 副式 | 解説 |  
|:--|:--|  
| `Distinct`  | SQLのDISTINCTと一緒です。  |  
| `Top<数字>` / `First<数字>`  |  先頭から`<数字>`レコード分を返します。 |  
| エンティティ名  | リレーション先のエンティティを返します。  |  
  
  
## 方法2 - `@Query`をつけたメソッドを実装  
  
例えば、このようになります。  
  
****  
```java:
@Query("SELECT u FROM User WHERE u.firstname = :firstname")
List<User> findByFirstname(String firstname);
```  
  
`@Query`の中身はJPQLでよいのかな？  
  
Spring Expression Languageも使えます。  
  
## 方法3 - Customクラスを作る  
  
命名規約やJPQLでは足りない機能を実装するときには、Customクラスを作ります。  
  
例えば、もともとこんなRepositoryインターフェースがあったとしたら、  
  
**UserRepository.java**  
```java:UserRepository.java
public interface UserRepository extends CrudRepository<User, Long> {}
```  
  
まずは独自の機能をもったインターフェースを作ります。  
  
**UserRepositoryCustom.java**  
```java:UserRepositoryCustom.java
public interface UserRepositoryCustom {
  public void executeSomeOperation(User user);
}
```  
  
もともとあったインターフェースに継承させます。  
  
**UserRepository.java**  
```java:UserRepository.java
public interface UserRepository extends CrudRepository<User, Long>, UserRepositoryCustom {}
```  
  
さらに実装を作ります。  
  
****  
```java:
public class UserRepostitoryImpl implements UserRepositoryCustom {
    public void executeSomeOperation(User user) {
       // なにか特別な処理
    }
}
```  
  
もう少し詳しく書きました。 ＞ [Customクラスを使用してデータベースアクセスする](http://qiita.com/sengoku/items/28b068e4ad8745340879)  
  
  
  
# テストする  
  
Repositoryインターフェースをテストします。  
  
テストにはHSQLデータベースを使用します。  
  
## pom.xmlを変更  
  
テストのスターター、JDBCドライバ~~、いつものjunit~~を依存ライブラリに追加します。  
  
**2016/11/11編集** junitとhamcrestは、スターターに入っていたので不要でした。  
  
**pom.xml**  
```xml:pom.xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.hsqldb</groupId>
            <artifactId>hsqldb</artifactId>
            <scope>test</scope>
        </dependency>
```  
  
## テスト用アプリケーションクラスの作成  
  
アプリケーションクラスがなければ作成します。  
  
**src/test/java/com/example/TestApplicationi.java**  
```java:src/test/java/com/example/TestApplicationi.java
package com.example;

import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;

@SpringBootApplication
@EnableJpaRepositories("com.example.repository")
public class TestApplication {
    
}
```  
  
普通は`@SpringBootApplication`だけで足りるのですが、Repositoryクラスが別にあるので`@EnableJpaRepositories`を付けています。  
  
## テストクラスの作成  
  
`@RunWith`を付けて、テストランナーを指定します。  
  
~~`@SpringBootTest`と`@Transactional`は何をやっているのかわかりません。~~  :sweat:  
  
**2016/11/17 追記** `@SpringBootTest`と`@Transactional`は不要でした。`@DataJpaTest`を付けるだけでよいです。これをつければSQLもログ出力してくれます。  
  
**src/test/java/com/example/repository/UserRepositoryTest.java**  
```java:src/test/java/com/example/repository/UserRepositoryTest.java
package com.example.repository;

import com.example.entity.User;
import java.util.Date;
import java.util.List;
import java.util.Optional;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import static org.junit.Assert.*;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.transaction.annotation.Transactional;

@RunWith(SpringRunner.class)
@DataJpaTest
public class UserRepositoryTest {
    
    @Autowired
    private UserRepository instance;
    
    @Before
    public void setUp() {
       // 初期データの用意等
    }

    @Test
    public void testFindSomething() {
       // テストを書く
    }
}
```  
  
  
# 参考リンク  
  
- [Spring Data JPA でのクエリー実装方法まとめ](http://qiita.com/tag1216/items/55742fdb442e5617f727)  
- [【Spring Data JPA】自動実装されるメソッドの命名ルール](http://qiita.com/sndr/items/af7d12be264c2cc4b252)  
- [Spring Data JPAリファレンスマニュアル](http://docs.spring.io/spring-data/data-jpa/docs/current/reference/html/)  
- [Spring Bootリファレンスマニュアル - JPA](http://docs.spring.io/spring-boot/docs/1.4.1.RELEASE/reference/htmlsingle/#boot-features-jpa-and-spring-data)  
- [Spring Data JPAソースコードレポジトリ](https://github.com/spring-projects/spring-data-jpa)  
  - [org.springframework.data.repository.query.parser.PartTree.java](https://github.com/spring-projects/spring-data-commons/blob/master/src/main/java/org/springframework/data/repository/query/parser/PartTree.java)   
- [Spring Frameworkリファレンスマニュアル - 統合テスト](http://docs.spring.io/spring/docs/4.3.x/spring-framework-reference/html/integration-testing.html)  
- [Testing improvements in Spring Boot 1.4](https://spring.io/blog/2016/04/15/testing-improvements-in-spring-boot-1-4)  
