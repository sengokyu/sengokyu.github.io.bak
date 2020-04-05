---
date: '2017-07-05T14:24:06+09:00'
layout: post
published: true
qiita_article_id: ac00699be59622a1040e
tags:
- Java
- Maven
title: '[Java][Maven] 3rdパーティのjarファイルをローカルレポジトリへ登録するpom.xmlの雛形'
updated: '2017-07-05T15:02:10+09:00'

---
Mavenレポジトリにない野良(?)jarファイルを、ローカルのレポジトリに登録するときに使うpom.xmlファイルの雛形です。  
  
  
**pom.xml**  
```xml:pom.xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId><!-- TODO:適当に --></groupId>
    <artifactId><!-- TODO:適当に --></artifactId>
    <version><!-- TODO:適当に --></version>
    <packaging>pom</packaging>
    <properties>
        <groupId><!-- TODO:登録したいgroupIdに置き換え -->p</groupId>
        <artifactId><!-- TODO:登録したいartifactIdに置き換え --></artifactId>
        <version><!-- TODO:登録したいversionに置き換え --></version>
        <packaging>jar</packaging>
        <file><!-- TODO:jarファイルの名前に置き換え --></file>
    </properties>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-install-plugin</artifactId>
                <version>2.5.2</version>
                <executions>
                    <execution>
                        <id>default-install</id>
                        <phase>none</phase>
                    </execution>
                    <execution>
                        <phase>install</phase>
                        <goals>
                            <goal>install-file</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```  
  
まいどまいどmvnコマンドの`install:install-file`ターゲットの使い方をぐぐるのは面倒  
  
グループメンバーにコマンド教えるのも面倒  
  
そんなときに使ってください。  
  
