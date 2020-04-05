---
date: '2015-02-16T13:56:32+09:00'
layout: post
published: true
qiita_article_id: bc85b5161de9839a865b
tags:
- Java
- JavaEE
- CDI
title: Arquillian+Glassfish Embedded Containerでは、ConversationScopedなbeanをインジェクトできない！？
updated: '2015-02-16T13:56:32+09:00'

---
# 環境 #  
  
 * JDK 1.8.0 u31 (x64)  
 * NetBeans IDE 8.0.2  
 * Arquillian core 1.1.7.Final  
 * Arquillian Glassfish container 1.0.0.CR4  
  
# ソース #  
  
こんな風にConversationScopedをつけたクラスを用意しました。  
  
**MyBean.java**  
```java:MyBean.java
@ConversationScoped
public class MyBean implements Serializable {
  // (略)
}
```  
  
上記クラスをテストするためのクラスを用意しました。  
  
**MyBeanTest.java**  
```java:MyBeanTest.java
@RunWith(Arqullian.class)
public class MyBeanTest {
    @Deployment
    public static Archive<?> createDeployment() {
        return ShrinkWrap.create(WebArchive.class).addClass(MyBean.class);
    }

    @Inject
    private MyBean myBean;
    // (略)
}

```  
  
# 結果 #  
  
テストを実行すると、MyBeanをインジェクトできなくて失敗します。  
  
****  
```text:
Caused by: org.jboss.weld.exceptions.IllegalArgumentException: WELD-001408: Unsatisfied dependencies for type MyBean with qualifiers @Default
  at injection point [BackedAnnotatedField] @Inject private MyBeanTest.instance
  at MyBeanTest.instance(MyBeanTest.java:0)
```  
  
# 考察 #  
  
こんなコードを付け加えてBeanManagerに取り扱ってもらえているかどうか確認すると、```MyBean```は入っていません。  
  
****  
```java:
BeanManager bm = CDI.current().getBeanManager();
Set<Bean<?>> beans = bm.getBeans(Object.class, new AnnotationLiteral<Any>(){});
for (Bean<?> bean:beans) {
    System.out.println(bean.getBeanClass().getName());
}
```  
  
ConversationScopedではなく、ApplicationScopedやRequestScopedなどではちゃんとインジェクトできます。  
  
何か追加で書かないといけないコードがあるのかもしれません。  
