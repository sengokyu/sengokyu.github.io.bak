---
date: '2015-03-25T16:20:50+09:00'
layout: post
published: true
qiita_article_id: 543e3ee309cb5705c08c
tags:
- Java
- JUnit
- JAXB
title: junit-addonsを入れるとJAXBが悲鳴をあげる
updated: '2015-03-25T16:20:50+09:00'

---
[JUnit-addons]:(http://sourceforge.net/projects/junit-addons/) を入れると、JAXBのunmarshalを呼んでいるところでこんな例外が発生します。  
  
```
java.lang.IllegalStateException: org.xml.sax.SAXNotRecognizedException: Feature 'http://javax.xml.XMLConstants/feature/secure-processing' is not recognized.
	at org.apache.xerces.parsers.AbstractSAXParser.setFeature(Unknown Source)
	at org.apache.xerces.jaxp.SAXParserImpl.setFeatures(Unknown Source)
	at org.apache.xerces.jaxp.SAXParserImpl.<init>(Unknown Source)
	at org.apache.xerces.jaxp.SAXParserFactoryImpl.newSAXParserImpl(Unknown Source)
	at org.apache.xerces.jaxp.SAXParserFactoryImpl.setFeature(Unknown Source)
	at com.sun.xml.bind.v2.util.XmlFactory.createParserFactory(XmlFactory.java:128)
	at com.sun.xml.bind.v2.runtime.unmarshaller.UnmarshallerImpl.getXMLReader(UnmarshallerImpl.java:154)
	at com.sun.xml.bind.v2.runtime.unmarshaller.UnmarshallerImpl.unmarshal(UnmarshallerImpl.java:291)
	at javax.xml.bind.JAXB.unmarshal(JAXB.java:186)
```  
  
いきなり大半のテストが動かなくなったんで、びっくりこきました。  
  
# 環境  
  
- Java 1.8  
- JUnit 4.11  
- JUnit-addons 1.4  
  
