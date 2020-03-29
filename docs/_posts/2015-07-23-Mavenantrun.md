---
'created_at: ': '2015-07-23T09:53:25+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Maven
      versions: []
  state: 0
title: "Maven antrun\u30D7\u30E9\u30B0\u30A4\u30F3\u3092\u5229\u7528\u3057\u3066\u3001\
  \u30C6\u30B9\u30C8\u6642\u306B\u30EA\u30BD\u30FC\u30B9\u30D5\u30A1\u30A4\u30EB\u3092\
  \u30B3\u30D4\u30FC\u3059\u308B\u3002"
'updated_at: ': '2015-07-23T09:53:25+09:00'

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
