---
date: '2015-07-23T09:53:25+09:00'
layout: post
published: true
qiita_article_id: 50004e9c30cfbc8a9e91
tags:
- Maven
title: Maven antrunプラグインを利用して、テスト時にリソースファイルをコピーする。
updated: '2015-07-23T09:53:25+09:00'

---
# 概略  
  
テスト実行前に``myproject.properties``ファイルを``myproject.ja_JP.properties``へコピーします。  
  
# なんでこんなことしているのか？  
  
ユニットテスト時に、どうしても各国語リソースが欲しい。  
↓  
とりあえず元ファイルをコピーしてしのぐ。  
↓  
元ファイルを変更すると、それを各国語へ反映しなくちゃいけない。  
↓  
面倒すぎる。 :disappointed:   
↓  
テスト時に元ファイルをコピーすればいいんじゃね？ ＜ イマココ！  
  
# antrunプラグインを使って、ファイルをコピーする  
  
resourcesプラグインには特定ファイルを複製する機能がないのです。 :disappointed_relieved:   
  
antrunプラグインを[resourcesプラグインのライフサイクルフェイズであるprocess-test-resources](http://maven.apache.org/plugins/maven-resources-plugin/testResources-mojo.html)フェイズで実行しています。  
  
テスト時のリソースコピー先は``${project.build.testOutputDirectory}``に入っているので、それをantのプロパティとして使えるようにしています。  
  
  
**pom.xml**  
```xml:pom.xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!-- (略)-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-antrun-plugin</artifactId>
                <version>1.8</version>
                <executions>
                    <execution>
                        <phase>process-test-resources</phase>
                        <configuration>
                            <target>
                                <property name="output_dir" value="${project.build.testOutputDirectory}"/>
                                <copy file="${output_dir}/myproject.properties"
                                      tofile="${output_dir}/myproject.ja_JP.properties" />
                            </target>
                        </configuration>
                        <goals>
                            <goal>run</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```  
  
これで、test-compileフェイズ実行時にリソースファイルがコピーされるようになりました。 :smile:   
