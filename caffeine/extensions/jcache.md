# JCache

JSR-107 JCache是一个标准化的缓存API,与Java EE 6兼容，并在JEE 8中引入。

Caffeine提供了一个本地内存中的实现.JCache提供使用[Typesafe Config](https://github.com/typesafehub/config)库进行配置的配置。有关更多详细信息，请参阅[reference.conf](https://github.com/ben-manes/caffeine/blob/master/jcache/src/main/resources/reference.conf)。 [FactoryCreator](https://github.com/ben-manes/caffeine/blob/master/jcache/src/main/java/com/github/benmanes/caffeine/jcache/configuration/TypesafeConfigurator.java#L111)可以被配置为将实例化委托给依赖注入框架。

> JCache设计的ExpiryPolicy延迟到期的条目,并依靠最大size约束住区条目.该规范的做饭与Caffeine的本地支持不兼容,在定期维护期间,这些支持将在o(1)时间内尽快的到期.规范期望所有用途都有附加的大小限制,急事规范不支持该功能.当使用JCache的过期版本,而不是Caffeine的坏味道.应该使用size的边界来避免内存泄漏,并及时通知监听者。

## 注解支持

### Spring

查看[Spring文档](https://spring.io/blog/2014/04/14/cache-abstraction-jcache-jsr-107-annotations-support)

从Spring Framework 4.3和Spring Boot 1.4开始支持Caffeine。可以查看[Spring Cache](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/cache.html).

### Guice

使用Guice注入的话:

```xml
compile 'org.jsr107.ri:cache-annotations-ri-guice:1.1.0'
```

```java
Injector injector = Guice.createInjector(new CacheAnnotationsModule());
```

### CDI

```xml
compile 'org.jsr107.ri:cache-annotations-ri-cdi:1.1.0'
```