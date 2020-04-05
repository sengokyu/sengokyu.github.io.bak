---
date: '2016-12-14T19:20:13+09:00'
layout: post
published: true
qiita_article_id: 1e9ae2218c06be0fecdb
tags:
- Java
- spring
- spring-boot
title: '[Java][Spring] Spring Boot依存性注入の不可解なハマリネタ'
updated: '2016-12-14T19:20:13+09:00'

---
わかっているひとにとっては当たり前なネタなのかもですが、大いに苦しめられたのでここにお披露目します。  
  
# 環境  
  
- Spring Boot 1.2.7  
- Java 1.8.0 102  
  
# 前提  
  
こんなレポジトリインターフェースたちがありました。  
  
- MenuRepository  
- CategoryRepository  
- UserRepository  
  
ユニットテストのため、各レポジトリのモックを返すConfigurationクラスを作りました。  
  
**MyConfig.java**  
```java:MyConfig.java
@Configuration
public class MyConfig {
    @Bean 
    public MenuRepository menuRepository() {
        return Mockito.mock(MenuRepository.class);
    }

    @Bean 
    public CategoryRepository categoryRepository() {
        return Mockito.mock(CategoryRepository.class);
    }

    @Bean 
    public UserRepository userRepository() {
        return Mockito.mock(UserRepository.class);
    }
}
```  
  
注入先のテストはこんなんです。  
  
**MyTest.java**  
```java:MyTest.java
// 略
public class MyTest {
    @Autowired
    private MenuRepository menuRepository;

    @Autowired
    private CategoryRepository categoryRepository;

    @Autowired
    private UserRepository userRepository;
}
```  
  
# 不可解な動き  
  
`categoryRepository`@Beanメソッドは呼ばれるのに、他の@Beanメソッドは呼び出されません。  
  
結果、注入先では`CategoryRepository`だけがモックになっているという。。。  
  
コンテナの中身を見ると、`CategoryRepository`だけ2つ（モックと元）あり、他は1つしかありません。  
  
  
# 解決策  
  
メソッド名を変えることで、無事すべての@Beanメソッドが呼び出されるようになりました。  
  
**MyConfig.java**  
```java:MyConfig.java
@Configuration
public class MyConfig {
    @Bean 
    @Primary
    public MenuRepository menuRepositoryBean() {
        return Mockito.mock(MenuRepository.class);
    }

    @Bean 
    @Primary
    public CategoryRepository categoryRepositoryBean() {
        return Mockito.mock(CategoryRepository.class);
    }

    @Bean 
    @Primary
    public UserRepository userRepositoryBean() {
        return Mockito.mock(UserRepository.class);
    }
}
```  
  
あと、型がかぶっているというエラーが出たので、`@Primary`を付けました。  
  
  
どうしてこうなるのでしょう……？？  
