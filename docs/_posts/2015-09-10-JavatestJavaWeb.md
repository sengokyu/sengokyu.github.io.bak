---
date: '2015-09-10T20:58:28+09:00'
layout: post
published: true
qiita_article_id: 6b8a7387f6241814f2b1
tags:
- Java
- testing
title: '[Java][test] Java Webテストツール文法まとめ'
updated: '2016-08-09T09:07:43+09:00'

---
# 概要  
  
http://java-source.net/open-source/web-testing-tools で紹介されているテストツールを使用したテストの書き方をまとめてみました。  
  
# HtmlUnit  
  
順当な感じです。  
  
****  
```java:
@Test
public void homePage() throws Exception {
    final WebClient webClient = new WebClient();
    final HtmlPage page = webClient.getPage("http://htmlunit.sourceforge.net");
    Assert.assertEquals("HtmlUnit - Welcome to HtmlUnit", page.getTitleText());

    final String pageAsXml = page.asXml();
    Assert.assertTrue(pageAsXml.contains("<body class=\"composite\">"));

    final String pageAsText = page.asText();
    Assert.assertTrue(pageAsText.contains("Support for the HTTP and HTTPS protocols"));

    webClient.closeAllWindows();
}
```  
  
# MaxQ  
  
プロキシ型なので割愛。  
  
# jWebUnit  
  
DSLっぽくていい感じ。  
  
****  
```java:
import org.junit.*;

import static net.sourceforge.jwebunit.junit.JWebUnit.*;

public class ExampleWebTestCase {

    @Before
    public void prepare() {
        setBaseUrl("http://localhost:8080/test");
    }

    @Test
    public void testLogin() {
        beginAt("/home");
        clickLink("login");
        assertTitleEquals("Login");
        setTextField("username", "test");
        setTextField("password", "test123");
        submit();
        assertTitleEquals("Welcome, test!");
    }
}
```  
  
# HttpUnit  
  
順当な感じ。  
  
****  
```java:
    WebConversation wc = new WebConversation();
    WebResponse   resp = wc.getResponse( "http://www.httpunit.org/doc/cookbook.html" ); // read this page
    WebLink       link = resp.getLinkWith( "response" );                                // find the link
    link.click();                                                                       // follow it
    WebResponse   jdoc = wc.getCurrentPage();                                           // retrieve the referenced page
```  
  
# Canoo WebTest  
  
XMLなんだ。。。ちょっとつらそう。  
  
****  
```xml:
<project default="test">
 <target name="test">

  <webtest 
       name="check that WebTest is Google's top 'WebTest' result">
    <invoke url="http://www.google.com/ncr" 
            description="Go to Google (in English)"/>
    <verifyTitle text="Google" />
    <setInputField name="q" value="WebTest" />
    <clickButton label="I'm Feeling Lucky" />
    <verifyTitle text="Canoo WebTest" />
  </webtest>

 </target>
</project>
```  
  
# JMeter  
  
負荷テストでした。  
  
# LoadSim  
  
負荷テストでした。  
  
# Lakta  
  
リンク切れ。  
  
# StoryTestMQ  
  
Seleniumを使用。  
  
# Slimdog  
  
リンク切れ。  
  
# Imprimatur  
  
リンク切れ。  
  
# slim  
  
リンク切れ。  
  
# Selenium  
  
****  
```java:
WebDriver driver = new InternetExplorerDriver();
driver.get("http://www.google.com");
driver.findElement(By.name("q")).sendKeys("Selenium");
driver.
```  
  
# Watij  
  
よさげ。  
  
****  
```java:
WebSpec spec = new WebSpec().mozilla();
spec.open("http://www.google.com");
spec.find.input().with.name("q").set.value("Watij");
spec.find.input().with.type("button").with.value("Google Search").click();
//check results
spec.find.a().with.href("http://watij.com/").shouldExist();
```  
  
  
