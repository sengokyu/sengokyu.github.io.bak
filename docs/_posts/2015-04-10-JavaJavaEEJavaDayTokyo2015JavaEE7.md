---
date: '2015-04-10T11:47:26+09:00'
layout: post
published: true
qiita_article_id: b1a5492819cfa946bb3f
tags:
- Java
- JavaEE
- CDI
title: '[Java][Java EE] Java Day Tokyo 2015 寺田佳央さん講演『Java EE 7エッセンシャルレシピ』まとめ'
updated: '2016-08-09T09:06:48+09:00'

---
**※この文章は講演をもとにしていますが、自分が理解できた内容をまとめているものです。文章中の誤り等はすべて自分に責任があります。**  
  
2015年4月8日に東京国際フォーラムで開催されたJava Day Tokyoに行ってきました。  
その中のセッションのひとつである寺田佳央さんによる『Java EE 7 エッセンシャルレシピ』を聞いてきましたので、なんとか追い付けた部分をまとめます。  
  
[公開されたスライド](http://www.slideshare.net/OracleMiddleJP/cdi-essential-receipe-at-java-day-tokyo-2015)とメモを見ながら、思い出して書いてます。  
  
  
# Managed Bean JSR-250  
  
Managed Beanはリソースの参照、インスタンスの生成、インスタンスの破棄に使用する。JSR-250にはいろいろアノテーションがあるけれど``@ManagedBean``アノテーションはほぼ使わない。  
  
## リソースの参照はアノテーションで  
  
### よろしくない書き方  
  
**ng.java**  
```java:ng.java
InitialContext id = new InitialContext();
ic.lookup("java:comp/DefaultManagedExecutorService");
```  
  
JNDIでlookupするのは好ましくない。  
  
### いまどきの書き方  
  
**ok.java**  
```java:ok.java
@Resource(name="comp/DefaultManagedExecutorService")
ManagedExecutorService managedExecSvc;
```  
  
``@Resource``アノテーションを使う。  
  
## コンストラクタは使わない  
  
コンストラクタ呼び出し時は、DI（依存性注入）が完了していないのでぬるぽになるかも。  
  
### いまどきの書き方  
  
**Foo.java**  
```java:Foo.java
public class Foo() {

  @PostConstruct
  public void init() {
    // ...
  }
}
```  
  
``@PostConstruct``をつけたメソッドの中でクラスを初期化する。  
  
  
# Interceptor JSR-318  
  
JavaでAOPするもの。EE5ではEJBのみで使えた。EE6では要XMLだった。EE7では``@Priority``をつけるだけでOK。  
  
Intercepterを使えば、ビジネスロジック以外の処理（ログ出力や実行時間計測など）をアノテーションに詰め込める。  
  
## 例えばメソッドの実行時間計測  
  
### よろしくない書き方  
  
**ng.java**  
```java:ng.java
public void executeSomeMethod1() {
 long start = System.currenTimeMillis();

 // 難しいビジネスロジック

 long end = System.currentTimeMillis();
 long time = end - start;
 logger.log(Level.DEBUG, "実行時間:" + time);
}
```  
  
メソッドの中にやりたいこと（ビジネスロジック）に直接関係ないコードが入り込んでいる。  
  
### いまどきの書き方  
  
**ok.java**  
```java:ok.java
@MySimpleProfilerInterceptor
public void executeSomeMethod1() {
  // 難しいビジネスロジック
}
```  
  
メソッドにアノテーションを付けるだけ、アノテーションの処理を別途書く。  
  
# Bean Validation JSR-349  
  
Beanにセットされる値を検証したいときは、ロジックで書かずアノテーションにする。  
  
### いまどきの書き方  
  
**Foo.java**  
```java:Foo.java
public class Foo {

  @NotNull
  @Size(min = 1)
  private String name;

  @NotNull
  @Size(max = 8)
  @Pattern("^\\d{3}-\\d{4}$")
  private String zipCode;
}
```  
  
# DI JSR-330  
  
EE環境では``@Inject``と``@Qualifier``アノテーションを使う。JSF関係のところで``@Named``を使う。  
  
## インスタンス生成ではnewを使わない  
  
newではクラスが密に結びつく。インスタンス生成はコンテナにまかせて``@Inject``で注入する。  
  
### よろしくない書き方  
  
**Foo.java**  
```java:Foo.java
public class Foo {

  public void listBar() {
    BarDAO = barDAO = new BarDAOFromCSV();
    // ...
  }
}
```  
  
BarDAOを他の実装に入れ替えたくてもできない。  
  
### いまどきの書き方  
  
**Foo.java**  
```java:Foo.java
public class Foo {

  @Inject
  private BarDAO barDAO;

  public void listBar() {
    // ...
  }
}
```  
  
あとからBarDAOを実装した何かに入れ替えられる。  
  
## 実装が複数あるときの区別の仕方  
  
例えば``Foo``を実装する``FooMan``と``FooWoman``があったとする。  
  
**FooMan.java**  
```java:FooMan.java
public class FooMan implements Foo {
  // ...
}
```  
  
**FooWoman.java**  
```java:FooWoman.java
public class FooWoman implements Foo {
  // ...
}
```  
  
### エラーになる書き方  
  
``Foo``を実装する2つのクラスがあると、下記の書き方では依存性を解決できないのでエラーになる。  
  
**Baz.java**  
```java:Baz.java
public class Baz {

  @Inject
  private Foo foo;
}
```  
  
### 解決策：Qualifierを作成して使う  
  
クラスを識別するQualifierを作成して、クラスと注入ポイントにアノテーションを付ける。  
  
注入するクラスにQualifierを付けて、、、  
  
**FooMan.java**  
```java:FooMan.java
@ManQualifier
public class FooMan implements Foo {
  // ...
}
```  
  
**FooWoman.java**  
```java:FooWoman.java
@WomanQualifier
public class FooWoman implements Foo {
  // ...
}
```  
  
注入ポイントにもQualifierを付ける。  
  
**Baz.java**  
```java:Baz.java
public class Baz {

  @ManQualifier
  @Inject
  private Foo fooMan;

  @WomanQualifier
  @Inject
  private Foo fooWoman;
}
```  
  
こうするとエラーにならず、期待したクラスが注入される。[^1]  
  
[^1]: クラスにQualifierを付けて識別したら後から実装を入れ替えたくても入れ替えられないし、疎結合にならないような……？？？  
  
## 独自Qualifierを作るときの注意  
  
命名規約を作るか、それ用のパッケージに入れるようにしないと、混乱するかも。  
  
## 不変(Immutable)なインスタンスが欲しいとき  
  
### エラーになる書き方  
  
注入ポイントは``final``にできないのでエラーになる。  
  
**FooService.java**  
```java:FooService.java
public class FooService {

  @Inject
  private final Foo foo;
}
```  
  
### 解決策：コンストラクタインジェクションを使う  
  
プロパティインジェクションはやめて、コンストラクタインジェクションにするとfinalが使える。  
  
**FooService.java**  
```java:FooService.java
public class FooService {

  private final Foo foo;

  @Inject
  public FooService(Foo foo) {
    this.foo = foo;
  }
}
```  
  
## 実装がたくさんありすぎてQualifierでは面倒なとき  
  
``@Named``アノテーションを使って区別するようにできるけど、ダメよ。ダメダメ。  
  
``@Named``はJSFやらのEL式に渡したいときに使う。  
  
# CDI JSR-346  
  
## CDIはデフォルト有効  
  
Java EE 7からデフォルトで有効になっている。  
  
Java EE 6は``beans.xml``というファイルを使っていた。Java EE 7では、アノテーションが付いているクラスがコンテナに載るようになる。  
  
コンテナに載せたいときは、下記のアノテーションを付ける。  
  
- ``@NormalScoped``を継承したもの  
- ``@Dependent``  
  
  
``beans.xml``で全クラスをコンテナに載せるようにもできるけれど、起動が遅くなるからダメよ。  
  
## CDI Managed Beanとは  
  
CDIコンテナによってProxyを自動生成されたbean。  
  
CDIはbeanのProxyクラスを自動生成する。注入ポイントに実際に注入されるのはProxyクラスのインスタンスとなる。  
  
## シングルトンしたいとき  
  
### よろしくない書き方  
  
``javax.inject.Singleton``は``@NormalScoped``継承ではないのでCDI Managed Beanにならない。  
  
**Ng.java**  
```java:Ng.java
@javax.inject.Singleton
public class Ng {
  // ...
}
```  
  
### 解決策：ApplicationScopedを使う  
  
**Ok.java**  
```java:Ok.java
@javax.enterprise.context.ApplicationScoped
public class Ok {
  // ...
}
```  
  
## 複数ある実装バージョンを配備時に切り替える  
  
クラスに``@Alternative``アノテーションを付けて、``beans.xml``ファイルで切り替える。  
  
**Foo1.java**  
```java:Foo1.java
@Alternative
@Dependent
public class Foo1 implements Foo {
  // ...
}
```  
  
**Foo2.java**  
```java:Foo2.java
@Alternative
@Dependent
public class Foo2 implements Foo {
  // ...
}
```  
  
``beans.xml``をクラスと同一アーカイブに入れる。  
  
**beans.xml**  
```xml:beans.xml
<beans xmlns="http://java.sun.com/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/beans_1_0.xsd">
    <alternatives>
        <class>Foo2</class>
    </alternatives>
</beans>
```  
  
Foo2が使われる。[^2]  
  
[^2]: Qualifierよりこちらがよいような。  
  
## beans.xmlよりQualifierが優先  
  
クラスにQualifierを付けると、``@Alternative``が有効にならず``beans.xml``の設定が無視される。  
  
クラスに``@Specialized``を付けるとよい。[^3]  
  
[^3]: このあたり意味わかってないです。  
  
  
## ``@Produces``でCDI Managed Bean以外も注入する  
  
やり方：  
  
1. Qualiferを作る  
1. インスタンスを提供するクラスを作る  
1. インスタンスを提供するメソッドに作成したQualiferと``@Produces``アノテーションを付ける  
  
## アノテーションたくさんつけるの面倒  
  
それStereotypeで解決するよ。  
  
### 面倒な書き方  
  
アノテーションがいっぱいあって忘れそう。  
  
**Stress.java**  
```java:Stress.java
@RequestScoped
@Foo
@Bar
@Baz
@Qux
public class Stress {
  // ...
}
```  
  
### 解決策：いつも使うアノテーションはStereotypeにまとめる  
  
Stereotypeを作成する。  
  
**MyStereotype.java**  
```java:MyStereotype.java
@Stereotype
@RequestScoped
@Foo
@Bar
@Baz
@Qux
@Target(TYPE)
@Retention(RUNTIME)
public @interface MyStereotype {}
```  
  
作成したStereotypeを使う。スッキリ。  
  
**Relax.java**  
```java:Relax.java
@MyStereotype
public class Relax {

  // ...
}
```  
  
## 組み込みのStereotypeがある  
  
``@Model``は、``@Named``と``@RequestScoped``と一緒。[^4]  
  
[^4]: きっとJSFのバッキングbean用かな。  
  
  
## 独自Stereotypeを作るときの注意  
  
命名規約を作るか、それ用のパッケージに入れるようにしないと、混乱するかも。  
  
## CDIでObserverパターン  
  
CDIならObserverパターンも簡単に実装できる。  
  
### イベント発行側  
  
``Event<?>``を注入し、fireメソッドを呼ぶ。  
  
**FireStartar.java**  
```java:FireStartar.java
@Dependent
public class FireStartar {

  @Inject
  Event<Foo> fooEvent; // 引数にしたいクラスを指定する

  public void executeSomething() {
    // イベント発火
    fooEvent.fire(new Foo());
  }
}
```  
  
### イベントを受信側  
  
``@Observes``を使う。  
  
**FireWorks.java**  
```java:FireWorks.java
@Dependent
public class FireWorks {

  public void startFire(@Observes Foo foo) {
    // ...
  }
}
```  
  
### CDIのイベントは同期処理  
  
イベントが終わるまで返ってこないので、重たい処理には不向き。  
  
代わりにEJBやJMSを使う。  
  
# 最後に：なんでもかんでもコンテナに載せない  
  
機能が必要なものだけ載せましょう。  
