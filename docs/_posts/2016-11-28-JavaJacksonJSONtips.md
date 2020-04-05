---
date: '2016-11-28T16:59:07+09:00'
layout: post
published: true
qiita_article_id: 33c6d76aee66cb93328a
tags:
- Java
- JSON
- Jackson
title: '[Java][Jackson] JSONシリアライズするけど、デシリアライズしないプロパティを定義するtips'
updated: '2016-11-28T16:59:07+09:00'

---
フィールドに`@JsonIgnore`アノテーションを付けます。  
ゲッターに`@JsonProperty`アノテーションを付けます。  
セッターに`@JsonIgnore`アノテーションを付けます。  
  
  
以下サンプルです。  
  
**SampleEntity.java**  
```java:SampleEntity.java
import com.fasterxml.jackson.annotation.JsonIgnore;
import com.fasterxml.jackson.annotation.JsonProperty;

public class SampleEntity implements Serializable {

    @JsonIgnore
    private Integer myId;

    @JsonProperty("myId")
    public Integer getMyId() { /* 省略 */ }

    @JsonIgnore
    public void setMyId(Integer myId) { /* 省略 */ }
}
```  
