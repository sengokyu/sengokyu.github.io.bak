---
date: '2017-02-20T11:43:53+09:00'
layout: post
published: true
qiita_article_id: e0175560d8694cf42cb0
tags:
- Java
- spring
- JAX-RS
- JavaEE
title: '[Java][Spring] Java EE(JAX-RS) vs. Spring REST実装比較メモ'
updated: '2017-02-20T11:43:53+09:00'

---
# クラスの実装  
  
## Java EE  
  
`ResourceConfig`を継承したクラスの中で、RESTリソースクラスが存在するパッケージを指定する。  
  
## Spring  
  
`@RestController`アノテーションを付ける  
  
# リクエスト受け付け部分  
  
|内容| Java EE | Spring | 備考 |  
|:--|:--|:--|:--|  
|メソッドの指定| `@GET`  | `@RequestMapping(method=RequestMethod.GET)`  | POST/DELETE/PUTも同様  |  
|パスの指定| `@Path("/path")`   | `@RequestMapping(value="/path")`  |   |  
|パラメータとしてのパスの指定| `@Path("{id:[0-9]+}")` | `@RequestMapping(value="{id:[0-9]+}")` | 可変部分は同様に{}で囲む。正規表現も使える。コロンの前後にスペースを入れてはいけません。 |  
|パス指定されたパラメータの受け付け| `@PathParam("id") Integer id`  | `@PathVariable(name="id") Integer id`  |   |  
|クエリ文字列の受け付け| `@QueryParam("name") String name`  | `@RequestParam(name="name",required=false) String name`  | Springは引数なしだと400 BAD REQUESTになるので、required=falseを付けます。  |  
|リクエスト本体の受け付け| とくになし。通常の引数のまま。 | `@RequestBody`を引数に付ける||  
  
# レスポンス出力部分  
  
  
|内容| Java EE | Spring | 備考 |  
|:--|:--|:--|:--|  
|コンテントタイプ指定| `@Produces(MediaType.APPLICATION_JSON)`   | `@RequestMapping(produces=MediaType.APPLICATION_JSON_VALUE)`   | Springの`MediaType`は、`org.springframework.http`パッケージ  |  
|200 OKを返す。|return Response.OK.build();|void||  
  
