---
'created_at: ': '2015-03-25T16:20:50+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Java
      versions: []
    - name: JUnit
      versions: []
    - name: JAXB
      versions: []
  state: 0
title: "junit-addons\u3092\u5165\u308C\u308B\u3068JAXB\u304C\u60B2\u9CF4\u3092\u3042\
  \u3052\u308B"
'updated_at: ': '2015-03-25T16:20:50+09:00'

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
  
