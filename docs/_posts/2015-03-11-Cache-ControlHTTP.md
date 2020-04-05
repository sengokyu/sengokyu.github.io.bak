---
date: '2015-03-11T10:38:42+09:00'
layout: post
published: true
qiita_article_id: 79bd10c2b0e04f38fd46
tags:
- Java
- JAX-RS
- JavaEE
title: アノテーションでCache-Control HTTPヘッダを制御する
updated: '2015-03-11T10:38:42+09:00'

---
# 概要  
  
JAX-RSリソースに ``@CacheControl(ほげほげ)`` のようなアノテーションを付与することで、レスポンスフィルタでCacheControl HTTPヘッダを出力します。  
  
## 使い方  
  
**SampleResource.java**  
```java:SampleResource.java
public class SampleResource {
    @GET
    @CacheControl(value=CacheResponseDirective.MustRevalidate, maxAge=0)
    public SomeEntity getEntity() {
    }
}
```  
  
上記リソースにアクセスすると、このようなHTTPヘッダが出力されます。  
  
```
Cache-Control: must-revalidate,max-age=0
```  
  
# 環境  
  
- Java 1.8 u31  
- Glassfish 4.1  
  
  
# コード  
  
## Cache-Controlヘッダのenum  
  
Cache-Controlヘッダで使えるものの一覧です。  
  
**CacheResponseDirective.java**  
```java:CacheResponseDirective.java
public enum CacheResponseDirective {

    Public("public"),
    Private("private"),
    NoCache("no-cache"),
    NoStore("no-store"),
    NoTransform("no-transform"),
    MustRevalidate("must-revalidate"),
    ProxyRevalidate("proxy-revalidate");

    private final String represent;

    private CacheResponseDirective(String represent) {
        this.represent = represent;
    }

    @Override
    public String toString() {
        return represent;
    }
}
```  
  
## アノテーション  
  
JAX-RSリソースに付けるアノテーションです。  
  
``@NameBinding``を付けたので、このアノテーションが付与されたリソースにのみフィルタが適用されます。  
  
**CacheControl.java**  
```java:CacheControl.java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import javax.ws.rs.NameBinding;

@NameBinding
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface CacheControl {

    CacheResponseDirective[] value() default CacheResponseDirective.Public;
    
    /**
     * max-ageをを指定。単位は秒。
     * <p>
     * 値が0以上の時出力します。
     * @return 
     */
    int maxAge() default -1;
    
    /**
     * s-maxageを指定。単位は秒。
     * <p>
     * 値が0以上の時出力します。
     * @return 
     */
    int sMaxage() default -1;
}
```  
  
## レスポンスフィルタ  
  
実際にCache-Controlヘッダを出力するフィルタです。  
  
**CacheControlFilter.java**  
```java:CacheControlFilter.java
import java.io.IOException;
import java.lang.annotation.Annotation;
import java.util.Arrays;
import java.util.Optional;
import javax.ws.rs.container.ContainerRequestContext;
import javax.ws.rs.container.ContainerResponseContext;
import javax.ws.rs.container.ContainerResponseFilter;
import javax.ws.rs.core.HttpHeaders;
import javax.ws.rs.ext.Provider;

@Provider
@CacheControl
public class CacheControlFilter implements ContainerResponseFilter {

    @Override
    public void filter(ContainerRequestContext request, ContainerResponseContext response) throws IOException {
        final Annotation[] annotations = response.getEntityAnnotations();
        final Optional<Annotation> optional = Arrays.stream(annotations)
                .filter(x -> x.annotationType() == CacheControl.class)
                .findFirst();

        if (optional.isPresent()) {
            final CacheControl annotation = (CacheControl) optional.get();
            final List<String> directives = Arrays.stream(annotation.value())
                    .map(x -> x.toString())
                    .collect(Collectors.toList());

            // max-ageやs-maxageは0以上の値が指定されたときに出力します。
            if (annotation.maxAge() >= 0) {
                directives.add(String.format("max-age=%d", annotation.maxAge()));
            }

            if (annotation.sMaxage() >= 0) {
                directives.add(String.format("s-maxage=%d", annotation.sMaxage()));
            }

            final String token = String.join(",", directives);

            response.getHeaders().putSingle(HttpHeaders.CACHE_CONTROL, token);
        }
    }
}

```  
