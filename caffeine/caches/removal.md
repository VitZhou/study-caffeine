# 移除(Removal)

术语:
- 驱逐(eviction)意味着由于策略的原因退出
- 失效(invalidation)表示手动移除
- 移除(removal)由驱逐和失效而产生的

## 显式的移除

在任何时候你都可以显式的使缓存中的条目失效,而无须等待条目由于策略被驱逐.

```java
// individual key
cache.invalidate(key)
// bulk keys
cache.invalidateAll(keys)
// all keys
cache.invalidateAll()
```

## 移除监听

```java
Cache<Key, Graph> graphs = Caffeine.newBuilder()
    .removalListener((Key key, Graph graph, RemovalCause cause) ->
        System.out.printf("Key %s was removed (%s)%n", key, cause))
    .build();
```
你可以通过Caffeine.removalListener(RemovalListener)为缓存指定一个移除监听器,以便在删除条目时执行某些操作. RemovalListener获取key，value和RemovalCause。

移除监听器是使用Executor异步执行的.默认执行程序是ForkJoinPool.commonPool(),可以通过Caffeine.executor(Executor)覆盖.当操作必须与删除同步执行时，请改为使用CacheWriter。

请注意，由RemovalListener抛出的任何异常都会被记录（使用Logger）并被吞下。