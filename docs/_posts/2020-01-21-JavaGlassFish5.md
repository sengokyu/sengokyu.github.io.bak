---
'created_at: ': '2020-01-21T18:16:29+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Java
      versions: []
    - name: glassfish
      versions: []
  state: 0
title: "[Java] GlassFish 5\u30C8\u30E9\u30D6\u30EB\u30B7\u30E5\u30FC\u30C8\u8A18\u9332"
'updated_at: ': '2020-01-21T18:16:29+09:00'

---
GlassFish 5を動かそうとして、意外にすんなりいかなかったのでトラブルシュートの記録です。  
  
# 環境  
  
OS: Azure上のWindows Server 2008 R2  
JDK: Zulu  
  
# トラブルシュート記録  
  
## [解決] `asadmin`コマンドを実行するとNullPointerException  
  
JDK 11 (Zulu 11.31.11)ではだめでした。  
JDK 8 (Zulu 8.44.0.9)を使うとOKでした。  
  
## [解決] `asadmin start-domain`するとNoClassDefFoundError例外  
  
`asadmin start-domain`とすると、こんなエラーになりました。  
  
```
Exception in thread "main" MultiException stack 1 of 2
java.lang.NoClassDefFoundError: com/sun/enterprise/admin/launcher/GFLauncherException
        at java.lang.Class.getDeclaredConstructors0(Native Method)
（略）
```  
  
すみません。GlassFish 5.0使ってました。  
Eclipseに移管したGlassFish 5.1を使いましょう。  
  
https://projects.eclipse.org/projects/ee4j.glassfish  
  
## [未解決] デプロイするとIllegalStateException  
  
warファイルをデプロイすると、このようなエラーが出ました。  
  
```
remote failure:
 Error occurred during deployment:
 Exception while loading the app :
 java.lang.IllegalStateException:
 ContainerBase.addChild:
 start:
 org.apache.catalina.LifecycleException:
 org.apache.catalina.LifecycleException:
 org.glassfish.jersey.server.model.ModelValidationException:
 Validation of the application resource model has failed during application initialization.
[[FATAL] No injection source found for a parameter of type public java.lang.String service.AddonFacadeREST.create(java.io.File,java.io.File,java.lang.String,javax.ws.rs.core.SecurityContext) at index 0.; source='ResourceMethod{httpMethod=PO
（略）
```  
  
GlassFish 5あきらめました。  
4.1.2を使うことにしました。快調です。  
