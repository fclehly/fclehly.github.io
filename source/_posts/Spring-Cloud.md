---
title: Spring Cloud
date: 2019-07-27 22:29:23
tags:
---

[坑](#坑)   
- [JAXB 异常](#jaxb-异常)

## 坑
### JAXB 异常
在运行spring-cloud-eureka-server时，会报错，日志打印错误栈如下：
```
java.lang.TypeNotPresentException: Type javax.xml.bind.JAXBContext not present
	at java.base/sun.reflect.generics.factory.CoreReflectionFactory.makeNamedType(CoreReflectionFactory.java:117) ~[na:na]
	at java.base/sun.reflect.generics.visitor.Reifier.visitClassTypeSignature(Reifier.java:125) ~[na:na]
	at java.base/sun.reflect.generics.tree.ClassTypeSignature.accept(ClassTypeSignature.java:49) ~[na:na]
	at java.base/sun.reflect.generics.visitor.Reifier.reifyTypeArguments(Reifier.java:68) ~[na:na]
	at java.base/sun.reflect.generics.visitor.Reifier.visitClassTypeSignature(Reifier.java:138) ~[na:na]
	at java.base/sun.reflect.generics.tree.ClassTypeSignature.accept(ClassTypeSignature.java:49) ~[na:na]
	at java.base/sun.reflect.generics.repository.ClassRepository.computeSuperInterfaces(ClassRepository.java:117) ~[na:na]
	at java.base/sun.reflect.generics.repository.ClassRepository.getSuperInterfaces(ClassRepository.java:95) ~[na:na]
	at java.base/java.lang.Class.getGenericInterfaces(Class.java:1138) ~[na:na]
	at com.sun.jersey.core.reflection.ReflectionHelper.getClass(ReflectionHelper.java:629) ~[jersey-core-1.19.1.jar:1.19.1]
	at com.sun.jersey.core.reflection.ReflectionHelper.getClass(ReflectionHelper.java:625) ~[jersey-core-1.19.1.jar:1.19.1]
	at com.sun.jersey.core.spi.factory.ContextResolverFactory.getParameterizedType(ContextResolverFactory.java:202) ~[jersey-core-1.19.1.jar:1.19.1]
	at com.sun.jersey.core.spi.factory.ContextResolverFactory.init(ContextResolverFactory.java:89) ~[jersey-core-1.19.1.jar:1.19.1]
	at com.sun.jersey.server.impl.application.WebApplicationImpl._initiate(WebApplicationImpl.java:1332) ~[jersey-server-1.19.1.jar:1.19.1]
	at com.sun.jersey.server.impl.application.WebApplicationImpl.access$700(WebApplicationImpl.java:180) ~[jersey-server-1.19.1.jar:1.19.1]
	at com.sun.jersey.server.impl.application.WebApplicationImpl$13.f(WebApplicationImpl.java:799) ~[jersey-server-1.19.1.jar:1.19.1]
	at com.sun.jersey.server.impl.application.WebApplicationImpl$13.f(WebApplicationImpl.java:795) ~[jersey-server-1.19.1.jar:1.19.1]
	at com.sun.jersey.spi.inject.Errors.processWithErrors(Errors.java:193) ~[jersey-core-1.19.1.jar:1.19.1]
	at com.sun.jersey.server.impl.application.WebApplicationImpl.initiate(WebApplicationImpl.java:795) ~[jersey-server-1.19.1.jar:1.19.1]
	at com.sun.jersey.server.impl.application.WebApplicationImpl.initiate(WebApplicationImpl.java:790) ~[jersey-server-1.19.1.jar:1.19.1]
	at com.sun.jersey.spi.container.servlet.ServletContainer.initiate(ServletContainer.java:509) ~[jersey-servlet-1.19.1.jar:1.19.1]
	at com.sun.jersey.spi.container.servlet.ServletContainer$InternalWebComponent.initiate(ServletContainer.java:339) ~[jersey-servlet-1.19.1.jar:1.19.1]
	at com.sun.jersey.spi.container.servlet.WebComponent.load(WebComponent.java:605) ~[jersey-servlet-1.19.1.jar:1.19.1]
	at com.sun.jersey.spi.container.servlet.WebComponent.init(WebComponent.java:207) ~[jersey-servlet-1.19.1.jar:1.19.1]
	at com.sun.jersey.spi.container.servlet.ServletContainer.init(ServletContainer.java:394) ~[jersey-servlet-1.19.1.jar:1.19.1]
	at com.sun.jersey.spi.container.servlet.ServletContainer.init(ServletContainer.java:744) ~[jersey-servlet-1.19.1.jar:1.19.1]
	at org.apache.catalina.core.ApplicationFilterConfig.initFilter(ApplicationFilterConfig.java:270) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.core.ApplicationFilterConfig.<init>(ApplicationFilterConfig.java:106) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.core.StandardContext.filterStart(StandardContext.java:4530) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.core.StandardContext.startInternal(StandardContext.java:5169) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:183) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.core.ContainerBase$StartChild.call(ContainerBase.java:1384) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.core.ContainerBase$StartChild.call(ContainerBase.java:1374) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264) ~[na:na]
	at org.apache.tomcat.util.threads.InlineExecutorService.execute(InlineExecutorService.java:75) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at java.base/java.util.concurrent.AbstractExecutorService.submit(AbstractExecutorService.java:140) ~[na:na]
	at org.apache.catalina.core.ContainerBase.startInternal(ContainerBase.java:909) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.core.StandardHost.startInternal(StandardHost.java:841) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:183) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.core.ContainerBase$StartChild.call(ContainerBase.java:1384) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.core.ContainerBase$StartChild.call(ContainerBase.java:1374) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264) ~[na:na]
	at org.apache.tomcat.util.threads.InlineExecutorService.execute(InlineExecutorService.java:75) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at java.base/java.util.concurrent.AbstractExecutorService.submit(AbstractExecutorService.java:140) ~[na:na]
	at org.apache.catalina.core.ContainerBase.startInternal(ContainerBase.java:909) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.core.StandardEngine.startInternal(StandardEngine.java:262) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:183) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.core.StandardService.startInternal(StandardService.java:421) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:183) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.core.StandardServer.startInternal(StandardServer.java:932) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:183) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.apache.catalina.startup.Tomcat.start(Tomcat.java:456) ~[tomcat-embed-core-9.0.21.jar:9.0.21]
	at org.springframework.boot.web.embedded.tomcat.TomcatWebServer.initialize(TomcatWebServer.java:105) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.web.embedded.tomcat.TomcatWebServer.<init>(TomcatWebServer.java:86) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.web.embedded.tomcat.TomcatServletWebServerFactory.getTomcatWebServer(TomcatServletWebServerFactory.java:414) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.web.embedded.tomcat.TomcatServletWebServerFactory.getWebServer(TomcatServletWebServerFactory.java:178) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.createWebServer(ServletWebServerApplicationContext.java:179) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.onRefresh(ServletWebServerApplicationContext.java:152) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:543) ~[spring-context-5.1.8.RELEASE.jar:5.1.8.RELEASE]
	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:140) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:742) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:389) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:311) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1213) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1202) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at com.example.eurekaserver.EurekaServerApplication.main(EurekaServerApplication.java:12) ~[classes/:na]
Caused by: java.lang.ClassNotFoundException: javax.xml.bind.JAXBContext
	at java.base/jdk.internal.loader.BuiltinClassLoader.loadClass(BuiltinClassLoader.java:583) ~[na:na]
	at java.base/jdk.internal.loader.ClassLoaders$AppClassLoader.loadClass(ClassLoaders.java:178) ~[na:na]
	at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:521) ~[na:na]
	at java.base/java.lang.Class.forName0(Native Method) ~[na:na]
	at java.base/java.lang.Class.forName(Class.java:398) ~[na:na]
	at java.base/sun.reflect.generics.factory.CoreReflectionFactory.makeNamedType(CoreReflectionFactory.java:114) ~[na:na]
	... 65 common frames omitted

2019-07-27 22:34:12.507 ERROR 19315 --- [           main] o.apache.catalina.core.StandardContext   : One or more Filters failed to start. Full details will be found in the appropriate container log file
2019-07-27 22:34:12.508 ERROR 19315 --- [           main] o.apache.catalina.core.StandardContext   : Context [] startup failed due to previous errors
2019-07-27 22:34:12.511  WARN 19315 --- [           main] o.a.c.loader.WebappClassLoaderBase       : The web application [ROOT] appears to have started a thread named [spring.cloud.inetutils] but has failed to stop it. This is very likely to create a memory leak. Stack trace of thread:
 java.base@11.0.2/jdk.internal.misc.Unsafe.park(Native Method)
 java.base@11.0.2/java.util.concurrent.locks.LockSupport.park(LockSupport.java:194)
 java.base@11.0.2/java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(AbstractQueuedSynchronizer.java:2081)
 java.base@11.0.2/java.util.concurrent.LinkedBlockingQueue.take(LinkedBlockingQueue.java:433)
 java.base@11.0.2/java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:1054)
 java.base@11.0.2/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1114)
 java.base@11.0.2/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)
 java.base@11.0.2/java.lang.Thread.run(Thread.java:834)
2019-07-27 22:34:12.540  INFO 19315 --- [           main] o.apache.catalina.core.StandardService   : Stopping service [Tomcat]
2019-07-27 22:34:12.546  WARN 19315 --- [           main] ConfigServletWebServerApplicationContext : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.context.ApplicationContextException: Unable to start web server; nested exception is org.springframework.boot.web.server.WebServerException: Unable to start embedded Tomcat
2019-07-27 22:34:12.557  INFO 19315 --- [           main] ConditionEvaluationReportLoggingListener : 

Error starting ApplicationContext. To display the conditions report re-run your application with 'debug' enabled.
2019-07-27 22:34:12.564 ERROR 19315 --- [           main] o.s.boot.SpringApplication               : Application run failed

org.springframework.context.ApplicationContextException: Unable to start web server; nested exception is org.springframework.boot.web.server.WebServerException: Unable to start embedded Tomcat
	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.onRefresh(ServletWebServerApplicationContext.java:155) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:543) ~[spring-context-5.1.8.RELEASE.jar:5.1.8.RELEASE]
	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:140) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:742) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:389) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:311) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1213) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1202) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at com.example.eurekaserver.EurekaServerApplication.main(EurekaServerApplication.java:12) ~[classes/:na]
Caused by: org.springframework.boot.web.server.WebServerException: Unable to start embedded Tomcat
	at org.springframework.boot.web.embedded.tomcat.TomcatWebServer.initialize(TomcatWebServer.java:124) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.web.embedded.tomcat.TomcatWebServer.<init>(TomcatWebServer.java:86) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.web.embedded.tomcat.TomcatServletWebServerFactory.getTomcatWebServer(TomcatServletWebServerFactory.java:414) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.web.embedded.tomcat.TomcatServletWebServerFactory.getWebServer(TomcatServletWebServerFactory.java:178) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.createWebServer(ServletWebServerApplicationContext.java:179) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.onRefresh(ServletWebServerApplicationContext.java:152) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	... 8 common frames omitted
Caused by: java.lang.IllegalStateException: StandardEngine[Tomcat].StandardHost[localhost].TomcatEmbeddedContext[] failed to start
	at org.springframework.boot.web.embedded.tomcat.TomcatWebServer.rethrowDeferredStartupExceptions(TomcatWebServer.java:169) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	at org.springframework.boot.web.embedded.tomcat.TomcatWebServer.initialize(TomcatWebServer.java:108) ~[spring-boot-2.1.6.RELEASE.jar:2.1.6.RELEASE]
	... 13 common frames omitted

```
可以看到主要的错误信息是：`java.lang.TypeNotPresentException: Type javax.xml.bind.JAXBContext not present`。

这是因为java11把`java.xml.bind`从classpath中移除了：
* java7: include
* java8: include
* java9: deprecated
* java10: deprecated
* java11: removed
因此需要在依赖中手动引入`JAXB`依赖，maven的`pom.xml`添加的依赖如下：
```xml
<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-core</artifactId>
    <version>2.3.0.1</version>
</dependency>
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
    <version>2.3.1</version>
</dependency>
<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-impl</artifactId>
    <version>2.3.1</version>
</dependency>
```