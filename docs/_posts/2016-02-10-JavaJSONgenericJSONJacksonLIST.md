---
'created_at: ': '2016-02-10T15:46:31+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Java
      versions: []
    - name: Jackson
      versions: []
  state: 0
title: "[Java][JSON] generic\u306A\u30AF\u30E9\u30B9\u306E\u914D\u5217\u3068\u306A\
  \u3063\u3066\u3044\u308BJSON\u3092Jackson\u3092\u4F7F\u3063\u3066LIST\u3078\u30C7\
  \u30B7\u30EA\u30A2\u30E9\u30A4\u30BA\u3059\u308B\u65B9\u6CD5"
'updated_at: ': '2016-08-09T09:08:43+09:00'

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
