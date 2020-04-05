---
date: '2014-11-14T17:48:25+09:00'
layout: post
published: true
qiita_article_id: 237e7587be3674f82334
tags:
- glassfish
- CDI
- jersey
title: Glassfish 4.1において、JerseyリソースにCDIでインジェクトできない。というハマリの記録
updated: '2014-11-14T21:03:23+09:00'

---
# 概要  
  
JAX-RSのリソースクラスにCDIの`@inject`アノテーションを使用してオブジェクトを注入していると実行時に例外が出ます。  
  
# 環境  
  
Glassfish 4.1  
Java 1.8.0 u25 (x64)  
  
# ソースコード  
  
こんなJerseyリソースクラスがあります。  
  
**GenericResource.java**  
```java:GenericResource.java
import javax.inject.Inject;
import javax.ws.rs.Produces;
import javax.ws.rs.GET;
import javax.ws.rs.Path;

@Path("/")
public class GenericResource {

    @Inject
    private UserSession userSession;
    
    @GET
    @Produces("text/plain")
    public String getText() {
        return "Hello " + userSession.getUserName();
    }
}
```  
  
上記クラスへインジェクトしたいクラスがあります。  
  
**UserSession.java**  
```java:UserSession.java
import java.io.Serializable;
import javax.annotation.PostConstruct;
import javax.enterprise.context.SessionScoped;

@SessionScoped
public class UserSession implements Serializable {
    private static final long serialVersionUID = 1L;
    private String userName;

    @PostConstruct
    public void init() {
        this.setUserName(String.valueOf(Math.random()));
    }
    
    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }
}
```  
  
アプリケーションクラスはこんなんです。  
  
**ApplicationConfig.java**  
```java:ApplicationConfig.java
@javax.ws.rs.ApplicationPath("/")
public class ApplicationConfig extends Application {

    @Override
    public Set<Class<?>> getClasses() {
        Set<Class<?>> resources = new java.util.HashSet<>();
        addRestResourceClasses(resources);
        return resources;
    }

    /**
     * Do not modify addRestResourceClasses() method.
     * It is automatically populated with
     * all resources defined in the project.
     * If required, comment out calling this method in getClasses().
     */
    private void addRestResourceClasses(Set<Class<?>> resources) {
        resources.add(com.example.GenericResource.class);
    }
    
}
```  
WEB-INF/beans.xmlファイルは無しです。  
  
# 実行結果  
  
デプロイは成功しますが、実行時に落ちます。  
  
```text
Warning:   The following warnings have been detected: WARNING: Unknown HK2 failure detected:
MultiException stack 1 of 1
org.glassfish.hk2.api.UnsatisfiedDependencyException: There was no object available for injection at SystemInjecteeImpl(requiredType=UserSession,parent=GenericResource,qualifiers={},position=-1,optional=false,self=false,unqualified=null,1850411141)
	at org.jvnet.hk2.internal.ThreeThirtyResolver.resolve(ThreeThirtyResolver.java:74)
	at org.jvnet.hk2.internal.Utilities.justInject(Utilities.java:947)
	at org.jvnet.hk2.internal.ServiceLocatorImpl.inject(ServiceLocatorImpl.java:902)
	at org.glassfish.jersey.gf.cdi.internal.CdiComponentProvider$CdiFactory$2.getInstance(CdiComponentProvider.java:245)
	at org.glassfish.jersey.gf.cdi.internal.CdiComponentProvider$CdiFactory.provide(CdiComponentProvider.java:189)
	at org.jvnet.hk2.internal.FactoryCreator.create(FactoryCreator.java:124)
	at org.jvnet.hk2.internal.SystemDescriptor.create(SystemDescriptor.java:461)
	at org.jvnet.hk2.internal.PerLookupContext.findOrCreate(PerLookupContext.java:69)
	at org.jvnet.hk2.internal.Utilities.createService(Utilities.java:2258)
	at org.jvnet.hk2.internal.ServiceLocatorImpl.internalGetService(ServiceLocatorImpl.java:690)
	at org.jvnet.hk2.internal.ServiceLocatorImpl.getService(ServiceLocatorImpl.java:655)
	at org.glassfish.jersey.internal.inject.Injections.getOrCreate(Injections.java:172)
	at org.glassfish.jersey.server.model.MethodHandler$ClassBasedMethodHandler.getInstance(MethodHandler.java:185)
	at org.glassfish.jersey.server.internal.routing.PushMethodHandlerRouter.apply(PushMethodHandlerRouter.java:74)
	at org.glassfish.jersey.server.internal.routing.RoutingStage._apply(RoutingStage.java:112)
	at org.glassfish.jersey.server.internal.routing.RoutingStage._apply(RoutingStage.java:115)
	at org.glassfish.jersey.server.internal.routing.RoutingStage._apply(RoutingStage.java:115)
	at org.glassfish.jersey.server.internal.routing.RoutingStage._apply(RoutingStage.java:115)
	at org.glassfish.jersey.server.internal.routing.RoutingStage.apply(RoutingStage.java:94)
	at org.glassfish.jersey.server.internal.routing.RoutingStage.apply(RoutingStage.java:63)
	at org.glassfish.jersey.process.internal.Stages.process(Stages.java:197)
	at org.glassfish.jersey.server.ServerRuntime$1.run(ServerRuntime.java:263)
	at org.glassfish.jersey.internal.Errors$1.call(Errors.java:271)
	at org.glassfish.jersey.internal.Errors$1.call(Errors.java:267)
	at org.glassfish.jersey.internal.Errors.process(Errors.java:315)
	at org.glassfish.jersey.internal.Errors.process(Errors.java:297)
	at org.glassfish.jersey.internal.Errors.process(Errors.java:267)
	at org.glassfish.jersey.process.internal.RequestScope.runInScope(RequestScope.java:297)
	at org.glassfish.jersey.server.ServerRuntime.process(ServerRuntime.java:254)
	at org.glassfish.jersey.server.ApplicationHandler.handle(ApplicationHandler.java:1028)
	at org.glassfish.jersey.servlet.WebComponent.service(WebComponent.java:372)
	at org.glassfish.jersey.servlet.ServletContainer.service(ServletContainer.java:381)
	at org.glassfish.jersey.servlet.ServletContainer.service(ServletContainer.java:344)
	at org.glassfish.jersey.servlet.ServletContainer.service(ServletContainer.java:221)
	at org.apache.catalina.core.StandardWrapper.service(StandardWrapper.java:1682)
	at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:318)
	at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:160)
	at org.apache.catalina.core.StandardPipeline.doInvoke(StandardPipeline.java:734)
	at org.apache.catalina.core.StandardPipeline.invoke(StandardPipeline.java:673)
	at com.sun.enterprise.web.WebPipeline.invoke(WebPipeline.java:99)
	at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:174)
	at org.apache.catalina.connector.CoyoteAdapter.doService(CoyoteAdapter.java:415)
	at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:282)
	at com.sun.enterprise.v3.services.impl.ContainerMapper$HttpHandlerCallable.call(ContainerMapper.java:459)
	at com.sun.enterprise.v3.services.impl.ContainerMapper.service(ContainerMapper.java:167)
	at org.glassfish.grizzly.http.server.HttpHandler.runService(HttpHandler.java:201)
	at org.glassfish.grizzly.http.server.HttpHandler.doHandle(HttpHandler.java:175)
	at org.glassfish.grizzly.http.server.HttpServerFilter.handleRead(HttpServerFilter.java:235)
	at org.glassfish.grizzly.filterchain.ExecutorResolver$9.execute(ExecutorResolver.java:119)
	at org.glassfish.grizzly.filterchain.DefaultFilterChain.executeFilter(DefaultFilterChain.java:284)
	at org.glassfish.grizzly.filterchain.DefaultFilterChain.executeChainPart(DefaultFilterChain.java:201)
	at org.glassfish.grizzly.filterchain.DefaultFilterChain.execute(DefaultFilterChain.java:133)
	at org.glassfish.grizzly.filterchain.DefaultFilterChain.process(DefaultFilterChain.java:112)
	at org.glassfish.grizzly.ProcessorExecutor.execute(ProcessorExecutor.java:77)
	at org.glassfish.grizzly.nio.transport.TCPNIOTransport.fireIOEvent(TCPNIOTransport.java:561)
	at org.glassfish.grizzly.strategies.AbstractIOStrategy.fireIOEvent(AbstractIOStrategy.java:112)
	at org.glassfish.grizzly.strategies.WorkerThreadIOStrategy.run0(WorkerThreadIOStrategy.java:117)
	at org.glassfish.grizzly.strategies.WorkerThreadIOStrategy.access$100(WorkerThreadIOStrategy.java:56)
	at org.glassfish.grizzly.strategies.WorkerThreadIOStrategy$WorkerThreadRunnable.run(WorkerThreadIOStrategy.java:137)
	at org.glassfish.grizzly.threadpool.AbstractThreadPool$Worker.doWork(AbstractThreadPool.java:565)
	at org.glassfish.grizzly.threadpool.AbstractThreadPool$Worker.run(AbstractThreadPool.java:545)
	at java.lang.Thread.run(Thread.java:745)
```  
  
# 解決策（？）  
  
`@Inject`アノテーションを`@Context`アノテーションに変えると動きます。  
  
**ウソです。NULLポで落ちます**  
  
注入したいクラスを`@Stateless`にして、`@Inject`を`@EJB`に変えると動きます。  
  
# まさかの解決策（？）  
  
`private UserSession userSession`を`UserSession userSesion`にします。つまり`private`をとるだけ！！！  
（号泣）  
  
# 考察  
  
検索すると同様の問題はいくつもあるので、やってることが間違っているのかも。。。  
  
[ここ][link1] によるとJerseyリソースはContextをインジェクトするため、CDIとは別のコンテナの上で動いているからダメだとか？？？  
  
# 参考  
  
* http://qiita.com/opengl-8080/items/cfdde684b36efec50477  
* https://java.net/jira/browse/JERSEY-2358  
* https://java.net/jira/browse/GLASSFISH-20597  
* [GlassFish 4.0のCDIに潜む罠](http://www.coppermine.jp/docs/programming/2013/12/glassfish4-cdi.html)  
* [CDI interceptor injected into a jersey RS service(resource) not working?][link1]  
  
  
[link1]:http://stackoverflow.com/questions/21295589/cdi-interceptor-injected-into-a-jersey-rs-serviceresource-not-working  
  
  
  
