---
'created_at: ': '2016-11-14T11:11:06+09:00'
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
    - name: spring-boot
      versions: []
    - name: spring-data-jpa
      versions: []
  state: 0
title: "[Java][Spring Boot][JPA] \u30C6\u30B9\u30C8\u6642\u306BSQL\u3092\u30ED\u30B0\
  \u51FA\u529B\u3059\u308B \uFF70 NetBeans\u3067\u59CB\u3081\u308BSpring Boot(8)"
'updated_at: ': '2016-11-18T11:49:04+09:00'

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
