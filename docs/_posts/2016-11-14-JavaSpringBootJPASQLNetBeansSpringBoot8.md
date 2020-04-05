---
date: '2016-11-14T11:11:06+09:00'
layout: post
published: true
qiita_article_id: c62b53101c837776f110
tags:
- Java
- spring
- Hibernate
- spring-boot
- spring-data-jpa
title: '[Java][Spring Boot][JPA] テスト時にSQLをログ出力する ｰ NetBeansで始めるSpring Boot(8)'
updated: '2016-11-18T11:49:04+09:00'

---
hibernateのログレベルを変更する設定ファイルを作成します。  
  
**src/test/resources/application.yml**  
```yaml:src/test/resources/application.yml
logging:
    level:
        org:
            hibernate:
                SQL: DEBUG
                type:
                    descriptor:
                        sql:
                            BasicBinder: TRACE
```  
  
テストを実行すれば、その他のログと一緒にSQLが出力されます。  
  
![Screenshot from 2016-11-14 11-10-12.png](/assets/images/dacf71be-4b3b-3c1f-96f0-f1f879fc3c01.png)  
  
----  
  
**2016/11/17追記**  
  
設定ファイルを作成しなくても、テストクラスのアノテーションを変更するだけでいけました。  
  
  
このようになっていたアノテーションを  
  
**MyTest.java**  
```java:MyTest.java
@RunWith(SpringRunner.class)
@SpringBootTest
@Transactional
public class MyTest {
    // テストコード
}
```  
  
このように`@DataJpaTest`アノテーションに変えるだけでした。  
  
**MyTest.java**  
```java:MyTest.java
@RunWith(SpringRunner.class)
@DataJpaTest
public class MyTest {
    // テストコード
}
```  
  
`@DataJpaTest`だけだと、バインディングされる値は出力されないので、それも見たいときは設定ファイルが必要です。  
  
**src/test/resources/application.yml**  
```yaml:src/test/resources/application.yml
logging:
    level:
        org:
            hibernate:
                type:
                    descriptor:
                        sql:
                            BasicBinder: TRACE
```  
  
  
  
  
# 環境  
  
- Spring Boot 1.4.2  
- Java 8  
