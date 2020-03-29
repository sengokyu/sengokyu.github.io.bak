---
'created_at: ': '2016-04-26T14:56:11+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Java
      versions: []
    - name: JCR
      versions: []
  state: 0
title: "Java Content Repository API (JSR-170, JSR-283)\u3068\u4EF2\u826F\u304F\u3057\
  \u305F\u3044"
'updated_at: ': '2016-04-26T14:56:11+09:00'

---
大嘘が書いてある可能性がありますので、薄目で読んでやってください。  
  
# はじめに  
  
CMS(Content Management System)を作りたいと思ったことがありますか？  
掘れば掘るほど要件が出てきて、めっさ実装が大変そうです。  
  
Javaには、そんなCMS開発に使えそうな標準API ～ JSR-170 Java Content Repository API 1.0 / JSR-283 Java Content Repository API 2.0 ～ があるそうです。  
  
# それってそもそも何よ？  
  
Wikipedia [Content Repository API for Java](https://en.wikipedia.org/wiki/Content_repository_API_for_Java) から引用します。  
  
> Content Repository API for Java (JCR) is a specification for a Java platform application programming interface (API) to access content repositories in a uniform manner. The content repositories are used in content management systems to keep the content data and also the metadata used in content management systems (CMS) such as versioning metadata.   
  
> コンテンツレポジトリAPI for Java (JCR)とは、統一されたやり方でコンテンツレポジトリへアクセスするためのJava向けAPIです。コンテンツレポジトリは、コンテンツマネジメントシステムでコンテンツデータやバージョン履歴メタデータなどを格納するために使われます。  
  
つまり、JCR APIを使えば、データベースアクセスとかファイルアクセスとかパーミッション管理とか面倒なことを考えなくてよくなるんんだよ！？  
  
な、なんだってーーー！！  
  
ってところでしょうか。  
  
そんなJCR APIを実装しているのが [Apache Jackrabbit](http://jackrabbit.apache.org/jcr/index.html) です。  
  
# JCR実装Jackrabbitを使ってみる  
  
それでは早速Jackrabbitを使ってみましょう。 [Getting Started](http://jackrabbit.apache.org/jcr/getting-started-with-apache-jackrabbit.html) をクリック！  
  
> To get started with Jackrabbit you should first become familiar with the JCR API.  
  
> Jackrabbitを使い始めるにあたっては、まずJCR APIに馴染んでください。  
  
ガッカリですよ！   
:astonished:   
  
続く。。。  
  
  
