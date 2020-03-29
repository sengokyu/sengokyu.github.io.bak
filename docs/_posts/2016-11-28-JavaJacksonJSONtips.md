---
'created_at: ': '2016-11-28T16:59:07+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Java
      versions: []
    - name: JSON
      versions: []
    - name: Jackson
      versions: []
  state: 0
title: "[Java][Jackson] JSON\u30B7\u30EA\u30A2\u30E9\u30A4\u30BA\u3059\u308B\u3051\
  \u3069\u3001\u30C7\u30B7\u30EA\u30A2\u30E9\u30A4\u30BA\u3057\u306A\u3044\u30D7\u30ED\
  \u30D1\u30C6\u30A3\u3092\u5B9A\u7FA9\u3059\u308Btips"
'updated_at: ': '2016-11-28T16:59:07+09:00'

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
