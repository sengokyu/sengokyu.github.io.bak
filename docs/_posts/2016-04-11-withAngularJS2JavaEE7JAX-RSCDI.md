---
date: '2016-04-11T21:19:40+09:00'
layout: post
published: true
qiita_article_id: 4f461dc23b2d02ae76bc
tags:
- Java
- JavaScript
- Angular
- JavaEE
- ズンドコキヨシ
title: （無駄に大仰な）ズンドコキヨシ with AngularJS 2, Java EE 7(JAX-RS, CDI)
updated: '2016-04-11T21:32:15+09:00'

---
ミニマムな方向はいろいろ出ているので、大仰な方向に走ってみました。  
[ズンドコキヨシまとめ](http://qiita.com/shunsugai@github/items/971a15461de29563bf90)  
  
# 概要  
  
AnguarJSからREST APIを叩いて「ズン」または「ドコ」の取得を繰り返し、「ズンズンズンズンドコ」が並んだら「キ・ヨ・シ！」と表示します。  
  
# 仕様  
  
## サーバ側  
  
JAX-RSリソース`/webapi/zundoko`へGETアクセスするとJSONが返る。  
  
JSONの内容はこんな感じにします。  
  
****  
```json:
{
  zundoko: 'zun'
}
```  
  
または  
  
****  
```json:
{
  zundoko: 'doko'
}
```  
  
毎回異なる結果が返るという、全然RESTじゃない仕様 :sweat_smile:   
  
## クライアント側  
  
`zundoko`ディレクティブを作成する。  
  
*n*ミリ秒ごとにズンドコリソースへアクセスし、取得結果を表示する。  
  
直近5つが**例の並び**なら「キ・ヨ・シ！」と表示する。  
  
せっかくなのでi18n化 :wink:  
  
# プロジェクト準備～ :smile:   
  
このプロジェクトで使うものです。  
  
| 項目 | 使うもの |  
|-----|--------------|  
| JDK | Oracle JDK 8 |  
| 統合開発環境 | NetBeans |  
| 依存性管理/ビルドツール | Maven |  
| Javaフレームワーク | Java EE 7 |  
| Java EEサーバ | Glassfish 4.1.1 |  
| Javaテストツール | junit、Arquillian |  
| JavaScriptフレームワーク | AngularJS 2 |  
| JavaScriptテストツール | Google Chrome |   
  
  
# プロジェクト開始〜 :smile:  
  
## mavenを使ってArquillian[^1]のサンプルから開始  
  
[^1]: ArquillianはJava EEのテストフレームワークです。実際にJava EEサーバを起動するのでちと重たい。  
  
```
% mvn archetype:generate \
-DarchetypeArtifactId=javaee7-arquillian-archetype \
-DarchetypeGroupId=org.javaee-samples \
-DinteractiveMode=false -DgroupId=com.example -DartifactId=zundoko
```  
  
テスト用embeded glassfishのポート番号を変えます。デフォルトだとバッティングすることがあるので。  
  
**src/test/resources/arquillian.xml**  
```xml:src/test/resources/arquillian.xml
<?xml version="1.0" encoding="UTF-8"?>
<arquillian xmlns="http://jboss.org/schema/arquillian"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="
        http://jboss.org/schema/arquillian
        http://www.jboss.org/schema/arquillian/arquillian_1_0.xsd">
    <container qualifier="glassfish-embedded" default="true">
        <configuration>
            <property name="bindHttpPort">18181</property>
        </configuration>
    </container>
</arquillian>
```  
  
とりあえず雛形のテストを実行してみたり。ここでエラーだと、環境的に何か足りないかもです。  
  
```
% mvn -Pglassfish-embedded-arquillian test
```  
  
# ごいごいとサーバ側を実装  
  
## ZunDokoはEnumで  
  
ZunとDokoはEnumにしてみました。せっかくEnumにしたので、色気も出して`valueOf(boolearn)`なんてのを追加。  
  
**src/main/java/com/example/ZunDokoEnum.java**  
```java:src/main/java/com/example/ZunDokoEnum.java
public enum ZunDokoEnum {
    zun,
    doko;

    public static ZunDokoEnum valueOf(boolean b) {
        return b ? ZunDokoEnum.zun : ZunDokoEnum.doko;
    }
}
```  
  
## ZunDokoインスタンス  
  
JAX-RSから出力するクラスを作ります。  
  
**src/main/java/com/example/ZunDoko.java**  
```java:src/main/java/com/example/ZunDoko.java
public class ZunDoko {
    private ZunDokoEnum zunDoko;
    // コンストラクタやらsetterやらgetterやら
}
```  
  
## ZunDokoインスタンスの生成はファクトリクラスで  
  
二分の一の確率でズン・ドコを生成するファクトリクラスを作りました。  
  
なぜファクトリクラスを使うかといえば、ズンまたはドコを生成するという複雑なビジネスロジック :sparkles: をここへ閉じ込めるためです。  
  
**src/main/java/com/example/ZunDokoFactory.java**  
```java:src/main/java/com/example/ZunDokoFactory.java
public class ZunDokoFactory {

    private static final ZunDokoFactory SELF = new ZunDokoFactory();
    private final Random random = new Random();

    public static ZunDoko createZunDoko() {
        return SELF.create();
    }

    private ZunDoko create() {
        return new ZunDoko(ZunDokoEnum.valueOf(random.nextBoolean()));
    }
}
```  
  
テストも書きましょー。 :grin:   
  
**src/test/java/com/example/ZunDokoFactoryTest.java**  
```java:src/test/java/com/example/ZunDokoFactoryTest.java
public class ZunDokoFactoryTest {

    /**
     * Test of createZunDoko method, of class ZunDokoFactory.
     */
    @Test
    public void testCreateZunDoko() {
        System.out.println("===>>> createZunDoko");
        // 略
    }
}
```  
  
## あえてCDIを使う  
  
ZunDokoクラスのインスタンスは、CDIを使ってクラス外から注入することにします。  
  
インスタンス生成はファクトリクラスに任せたので、ファクトリクラスを呼ぶCDIプロデューサを作ります。  
  
### クオリファイアを作成  
  
DIコンテナが迷わずプロデューサを使ってくれるように、目印となるクオリファイアも作ります。  
  
**src/main/java/com/example/ZunDokoQualifier.java**  
```java:src/main/java/com/example/ZunDokoQualifier.java
@Qualifier
@Retention(RUNTIME)
@Target({METHOD, FIELD, PARAMETER, TYPE})
public @interface ZunDokoQualifier {
}
```  
  
### プロデューサを作成  
  
リクエストごとにズンドコインスタンスを生成するように、リクエストスコープにしてます。  
  
**src/main/java/com/example/ZunDokoProducer.java**  
```java:src/main/java/com/example/ZunDokoProducer.java
import javax.enterprise.inject.Produces;

@RequestScoped
public class ZunDokoProducer {

    @Produces
    @ZunDokoQualifier
    public ZunDoko produceZunDoko() {
        return ZunDokoFactory.createZunDoko();
    }
}
```  
  
### CDIのテスト  
  
Arquillianを使ってテストします。  
  
リアルにJava EEサーバが立ち上がって重たいので、CDI-Unit[^2]を使いたいところですが、依存性でArquillianと喧嘩するので混ぜないでおきます。  
  
[^2]: http://jglue.org/cdi-unit/  
  
ArquillianではいろいろなJava EEサーバを使えます[^3]。ここでは慣れたGlassfish embeddedを使いました。  
  
[^3]: 使用できるもの一覧 https://docs.jboss.org/author/display/ARQ/Container+adapters   
  
`pom.xml`の`glassfish-embedded-arquillian`プロファイルの内容を変更します。  
  
**pom.xml**  
```xml:pom.xml
      <profile>
         <id>glassfish-embedded-arquillian</id>
         <dependencies>
            <dependency>
               <groupId>org.jboss.arquillian.container</groupId>
               <artifactId>arquillian-glassfish-embedded-3.1</artifactId>
               <version>${version.arquillian_glassfish_embedded}</version>
               <scope>test</scope>
            </dependency>
            <dependency>
                <groupId>org.glassfish.main.extras</groupId>
                <artifactId>glassfish-embedded-all</artifactId>
                <version>${version.glassfish}</version>
                <scope>test</scope>
            </dependency>
         </dependencies>
      </profile>
```  
  
テストクラスを作成します。  
  
Java EEサーバにデプロイする必要分だけwarファイルを`@Deployment`アノテーションをつけたメソッドで作成します。  
  
`@Inject`、`@ZunDokoQualifier`を付けることで、プロデューサからZunDokoクラスのインスタンスが注入されます。  
  
**src/test/java/com/example/inject/ZunDokoProducerTest.java**  
```java:src/test/java/com/example/inject/ZunDokoProducerTest.java
@RunWith(Arquillian.class)
public class ZunDokoProducerTest {

    @Deployment
    public static Archive<?> createDeployment() {
        final WebArchive war = ShrinkWrap.create(WebArchive.class,
                ZunDokoProducerTest.class.getSimpleName() + ".war")
                .addClass(ZunDokoProducer.class)
                .addClass(ZunDokoFactory.class)
                .addClass(ZunDoko.class);
        return war;
    }

    @Inject
    @ZunDokoQualifier
    ZunDoko instance;

    // 略
}
```  
  
## JAX-RSリソースの作成  
  
CDIコンテナにのせるために`@Dependent`をつけます。  
  
**src/main/java/com/example/ZunDokoResource.java**  
```java:src/main/java/com/example/ZunDokoResource.java
package com.example;

import javax.enterprise.context.RequestScoped;
import javax.inject.Inject;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;

@Dependent
@Path("zundoko")
public class ZunDokoResource {

    @Inject
    private ZunDoko zunDoko;

    @GET
    @Produces(MediaType.APPLICATION_JSON)
    public ZunDoko getZunDoko() {
        return zunDoko;
    }
}
```  
  
### JAX-RSリソースのテスト  
  
Arquillianを使います。  
  
`@Deployment`アノテーションに`testable = false`引数を与えて、Webクライアントとして動作するテストにします。  
  
`setUp`メソッド内でWebクライアントを初期化しています。  
  
**src/test/java/com/example/ZunDokoResourceTest.java**  
```java:src/test/java/com/example/ZunDokoResourceTest.java
@RunWith(Arquillian.class)
public class ZunDokoResourceTest {

    @Deployment(testable = false)
    public static Archive<?> createDeployment() {
    // 略
    }

    @ArquillianResource
    private URI base;

    private WebTarget target;

    @Before
    public void setUp() {
        final Client client = ClientBuilder.newClient();
        target = client.target(base)
                .path("webapi")
                .register(ZunDoko.class);
    }


    @Test
    public void testGetZunDoko() {
        System.out.println("===>>> getZunDoko");
        // 略
    }
}
```  
  
  
# つづく。。。  
  
長くなりましたので、【クライアント側編】へ続きます。  
  
ソース全体はこちらに置いています。  
https://github.com/sengokyu/zundoko  
  
