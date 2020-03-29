---
'created_at: ': '2015-09-10T20:54:05+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Java
      versions: []
    - name: JAX-RS
      versions: []
    - name: JavaEE
      versions: []
    - name: jersey
      versions: []
  state: 0
title: "\u3010Jersey MVC Templates\u3011Viewable\u3092\u8FD4\u3059\u30EA\u30BD\u30FC\
  \u30B9\u3092\u30C6\u30B9\u30C8\u3059\u308B"
'updated_at: ': '2016-01-15T15:28:05+09:00'

---
# 概要  
  
## Jersey MVCを知っている人向け  
  
自前のTemplateProcessorを作って、テスト時はそいつから適当な出力をさせましょうというお話です。  
  
## Jersey MVCを知らない人向け  
  
Java EEでRESTを実現する仕様であるところのJAX-RSの実装のひとつに、Jerseyというものがあります。  
  
Jerseyには独自の拡張としてJersey MVC Templatesというものがあり、これを使うとMustach/Freemaker/JSPなどをテンプレートとして使用して出力を加工することができます。  
  
わりとよくある感じの手順でWebアプリを作れるわけです。  
  
# ソース  
  
Viewableを返すリソースクラスをテストするときは、このダミーのテンプレートプロセッサを使うようにApplicationクラスの中で指定してください。  
  
## ダミーのテンプレートプロセッサ  
  
**TestTemplateProcessor.java**  
```java:TestTemplateProcessor.java
@Provider
public class TestTemplateProcessor implements TemplateProcessor<String> {

    @Override
    public String resolve(String string, MediaType mt) {
        return string;
    }

    @Override
    public void writeTo(
            String t,
            Viewable vwbl,
            MediaType mt,
            MultivaluedMap<String, Object> mm,
            OutputStream out)
            throws IOException {

        final ObjectMapper objectMapper = new ObjectMapper();

        try (PrintStream ps = new PrintStream(out, true, "UTF-8")) {
            ps.print("path=");
            ps.print(t);
            ps.println();
            ps.print("model=");
            ps.print(vwbl.getModel().toString());
            ps.println();
            ps.print("refletion=");
            ps.println(ToStringBuilder.reflectionToString(vwbl.getModel()));
            ps.println();
            ps.print("json=");
            ps.println(objectMapper.writeValueAsString(vwbl.getModel()));
            ps.println();
        }
    }
}
```  
  
## Applicationクラスの例  
  
**MyTestApplication.java**  
```java:MyTestApplication.java
@ApplicationPath("res")
public class MyTestApplication extends ResourceConfig {

    public MyTestApplication() {
        super(MyTestTarget.class,
                MvcFeature.class,
                TestTemplateProcessor.class
        );
    }
}
```  
