---
date: '2016-12-13T21:47:55+09:00'
layout: post
published: true
qiita_article_id: d058b387e95858e16cf0
tags:
- Java
- spring
- spring-boot
title: '[Java][Spring] Spring Boot 1.4 -> 1.2 ダウングレードメモ'
updated: '2016-12-14T14:50:47+09:00'

---
こんなことをするひとはいないと思いますが、せっかく苦しんだのでメモしておきます。  
  
  
# この文章の表記について  
  
:grin: 1.4: 1.4の場合のやりかたです。  
:smirk: 1.2: 1.2の場合のやりかたです。  
  
# 実装関係  
  
## `@EntityScan`  
  
:grin: 1.4: org.springframework.boot.autoconfigure.domain.EntityScan  
:smirk: 1.2: org.springframework.boot.orm.jpa.EntityScan  
  
## DI  
  
:grin: 1.4: 自明であれば`@Autowired`を付けなくてもDIしてくます。  
:smirk: 1.2: `@Autowired`付けましょう。  
  
# テスト関係  
  
## テストランナー  
  
:grin: 1.4: `@RunWith(SpringRunner.class)`  
:smirk: 1.2: `@RunWith(SpringJUnit4ClassRunner.class)`  
  
## Springを使ったテスト  
  
:grin: 1.4: `@SpringBootTest`を付ける  
:smirk: 1.2: `@SpringApplicationConfiguration(classes = アプリケーションクラス)`を付ける  
  
http://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/SpringApplicationConfiguration.html  
  
## データベース統合テスト  
  
:grin: 1.4: `@DataJpaTest`をテストクラスに付ける。  
:smirk: 1.2: ちと大変。  
  
`@DataJpaTest`はこんなことをしてくれているそうです。  
  
 1. インメモリデータベースを設定  
 1. Hibernate/Spring Data/Datasourceを設定  
 1. `@EntityScan`を実行  
 1. SQLログを有効化  
  
全部がんばらないといけないかと思いましたが、これだけのことをやれば大丈夫そうです。  
  
 1. `@EntityScan`、`@EnableJpaRepositories`を付けたアプリケーションコンテキストクラスを作成。  
 2. `TestEntityManager`クラスを作成（1.4からぱくる）  
 3. `TestEntityManager`を返すBeanを作成（1.4からぱくる）  
 4. テストクラスに`@Transactional`と`@SpringApplicationConfiguration`を付ける。  
  
順番にやっていきます。  
  
### `@EntityScan`、`@EnableJpaRepositories`を付けたアプリケーションコンテキストクラスを作成  
  
**TestApplication.java**  
```java:TestApplication.java
@SpringBootApplication
@EnableJpaRepositories(レポジトリクラスの場所)
@EntityScan(エンティティクラスの場所)
@EnableJpaAuditing // これは今回関係ない
public class TestApplication {

}
```  
  
### `TestEntityManager`クラスを作成（1.4からぱくる）  
  
元ソースはここにあります。  
https://github.com/spring-projects/spring-boot/blob/master/spring-boot-test-autoconfigure/src/main/java/org/springframework/boot/test/autoconfigure/orm/jpa/  
  
**TestEntityManager.java**  
```java:TestEntityManager.java
public class TestEntityManager {

    private final EntityManagerFactory entityManagerFactory;

    public TestEntityManager(EntityManagerFactory entityManagerFactory) {
        Assert.notNull(entityManagerFactory, "EntityManagerFactory must not be null");
        this.entityManagerFactory = entityManagerFactory;
    }

    public <E> E persist(E entity) {
        getEntityManager().persist(entity);
        return entity;
    }

    public void flush() {
        getEntityManager().flush();
    }

    public <E> E persistAndFlush(E entity) {
        persist(entity);
        flush();
        return entity;
    }

    public void detach(Object entity) {
        getEntityManager().detach(entity);
    }

    public <E> E find(Class<E> entityClass, Object primaryKey) {
        return getEntityManager().find(entityClass, primaryKey);
    }

    public final EntityManager getEntityManager() {
        EntityManager manager = EntityManagerFactoryUtils
                .getTransactionalEntityManager(this.entityManagerFactory);
        Assert.state(manager != null, "No transactional EntityManager found");
        return manager;
    }
}
```  
  
### `TestEntityManager`を返すBeanを作成（1.4からぱくる）  
  
**TestEntityManagerConfigure.java**  
```Java:TestEntityManagerConfigure.java
@Configuration
@AutoConfigureAfter(HibernateJpaAutoConfiguration.class)
public class TestEntityManagerConfigure {

    @Bean
    @ConditionalOnMissingBean
    public TestEntityManager testEntityManager(
            EntityManagerFactory entityManagerFactory) {
        return new TestEntityManager(entityManagerFactory);
    }

}
```  
  
### テストクラスに`@Transactional`と`@SpringApplicationConfiguration`を付ける  
  
**MySomeRepositoryTest.java**  
```java:MySomeRepositoryTest.java
import org.springframework.transaction.annotation.Transactional;

@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(classes = TestApplication.class)
@Transactional
public class MySomeRepositoryTest {

    @Autowired
    private MySomeRepository instance;

    @Autowired
    private TestEntityManager em;

    @Test
    public void testSomeMethod {
     // ...
    }

}


```  
  
## モック化したインスタンスをインジェクトする  
  
:grin: 1.4: モック化したいインスタンス変数に`@MockBean`を付ける  
:smirk: 1.2: モック化したインスタンスを返す`@Bean`を定義して`@Primary`を付ける  
  
### 1.4の場合  
  
**MySomeTest.java**  
```java:MySomeTest.java
    @MockBean
    private MyTargetClass mock;
```  
  
### 1.2の場合  
  
特定の@Configurationクラスだけ有効になるように、@Profileを付けています。  
  
**MySomeTest.java**  
```java:MySomeTest.java
@ActiveProfiles("MySomeTest")
public class MySomeTest {
    @Autowired
    private MyTargetClass mock;
}
```  
  
**MySomeTestConfig.java**  
```java:MySomeTestConfig.java
@Configuration
@Profile("MySomeTest")
public class MySomeTestConfig {
    @Bean
    @Primary
    public MyTargetClass myTargetClass() {
        return Mockito.mock(MyTargetClass.class);
    }
}
```  
  
# 旅は続く。。。  
  
また何か発見があったら追記していきます。  
