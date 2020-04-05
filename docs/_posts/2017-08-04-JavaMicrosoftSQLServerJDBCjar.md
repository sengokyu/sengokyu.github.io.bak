---
date: '2017-08-04T11:15:20+09:00'
layout: post
published: true
qiita_article_id: c295bfb56d38773fda1c
tags:
- Java
- Maven
- Microsoft
- SQLServer
title: '[Java][Microsoft] SQL ServerのJDBCドライバをひとつのjarに含めるとき注意するべきこと'
updated: '2017-08-04T11:15:20+09:00'

---
# 前段  
  
[Maven Shadeプラグイン](https://maven.apache.org/components/plugins/maven-shade-plugin/index.html)便利ですね。  
長ったらしいクラスパスを書かなくても、jarファイル一発でプログラム起動できます。  
  
そんなjarファイルに、Microsoft SQL ServerのJDBCドライバを入れようとして起きたことです。  
  
  
# やったこと  
  
* sqljdbc4.jarをダウンロードしてきて、ローカルのMavenレポジトリに格納しました。  
* pom.xmlに先のsqljdbc4.jarへの依存性を記載しました。  
* shadeプラグインでひとつのjarにまとめるようにしました。  
  
  
# 起きたこと  
  
実行すると、こんなエラーで落ちました。  
  
```
Error: A JNI error has occurred, please check your installation and try again
Exception in thread "main" java.lang.SecurityException: Invalid signature file digest for Manifest main attributes 
```  
  
同じ問題にあたっているひと → https://social.msdn.microsoft.com/Forums/sqlserver/en-US/f5c69fee-8c68-4601-bfcb-376e21dfa809/sql-server-jdbc-javalangsecurityexception-invalid-signature-file-digest?forum=sqldataaccess  
  
# 原因  
  
sqljdbc4.jarの中に署名が入っているから？  
↑  
ぜんぜんわかってないです。  
  
# 解決策  
  
sqljdbc4.jarに含まれている署名ファイル(?)を、最終ｊａｒに含めないようにします。  
  
**pom.xml**  
```xml:pom.xml
(snip)
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
（snip）
                <configuration>
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
(snip)
```  
  
# ひとりごと  
  
Javaがjarファイルをどのように扱って実行しているのか、理解しないとですね。。。  
  
