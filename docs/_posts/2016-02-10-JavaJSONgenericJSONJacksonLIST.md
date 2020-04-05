---
date: '2016-02-10T15:46:31+09:00'
layout: post
published: true
qiita_article_id: 77de5e0a38e150747115
tags:
- Java
- Jackson
title: '[Java][JSON] genericなクラスの配列となっているJSONをJacksonを使ってLISTへデシリアライズする方法'
updated: '2016-08-09T09:08:43+09:00'

---
genericなクラスの場合は、下記にあるTypeReferenceを使う方法ではCastExceptionが発生してしまいました。  
  
- Jackson使い方メモ http://qiita.com/opengl-8080/items/b613b9b3bc5d796c840c  
- トップレベルが配列な JSON を、Jackson で POJO を含む List にデシリアライズする方法 http://qiita.com/komiya_atsushi/items/c978e85c2065f57cea38  
  
このようにすればよいみたいです。  
  
****  
```java:
public static <T extends Object> List<T> deserialize(final String jsonStr, final Class<T> clazz) {
  final ObjectMapper mapper = new ObjectMapper();
  final CollectionType jt
    = mapper.getTypeFactory().constructCollectionType(List.class, clazz);

  return (List<T>) mapper.readValue(jsonStr, jt);
}
```  
  
JacksonのFAQに記載ありました :laughing:   
http://wiki.fasterxml.com/JacksonFAQ#Deserializing_Generic_types  
