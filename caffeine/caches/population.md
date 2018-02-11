# 成员

Caffeine提供三种类型的成员加载策略：手动，同步加载和异步加载。

## 手动

```java
Cache<Key, Graph> cache = Caffeine.newBuilder()
    .expireAfterWrite(10, TimeUnit.MINUTES)
    .maximumSize(10_000)
    .build();
//查找条目,如果找不到则返回null
Graph graph = cache.getIfPresent(key);
//如果在缓存中不存在则查找并计算,如果不可计算则返回null
graph = cache.get(key, k -> createExpensiveGraph(key));
// 添加或更新条目
cache.put(key, graph);
// 移除条目
cache.invalidate(key);
```

Cache接口允许显示的控制检索,更新和移除条目

可以直接使用cache.put(key,value)将条目插入到缓存中.这将覆盖指定key在缓存中以前的条目.尽量使用cache.get(key,v->value)自动计算并将值插入缓存,以避免其他写入竞争.请注意,如果条目不可计算,则cache.get可能返回Null,如果计算失败,则可能会抛出异常。

也可以使用由cache.asMap()暴露的ConcurrentMap的任何方法对缓存进行更改。

## 同步加载

```java
LoadingCache<Key, Graph> cache = Caffeine.newBuilder()
    .maximumSize(10_000)
    .expireAfterWrite(10, TimeUnit.MINUTES)
    .build(key -> createExpensiveGraph(key));
//如果在缓存中不存在则查找并计算,如果不可计算则返回null
Graph graph = cache.get(key);
//查找并计算不存在的条目
Map<Key, Graph> graphs = cache.getAll(keys);
```

LoadingCache是使用附加的CacheLoader构建的缓存。

批量查找可以使用getAll方法执行。 默认情况下，getAll将对缓存中没有的每个key分别调用CacheLoader.load。 当批量检索比许多单独的查找更有效时，您可以重写CacheLoader.loadAll。

请注意，您可以编写一个CacheLoader.loadAll实现，为未特别请求的key加载值。 例如，如果计算某个组中的任何key的值将为该组中的所有key提供值，则loadAll可能会同时加载该组的其余部分。

## 异步加载

```java
AsyncLoadingCache<Key, Graph> cache = Caffeine.newBuilder()
    .maximumSize(10_000)
    .expireAfterWrite(10, TimeUnit.MINUTES)
    // Either: 使用包装为异步的同步计算进行构建
    .buildAsync(key -> createExpensiveGraph(key));
    // Or: 构建一个返回future的异步计算
    .buildAsync((key, executor) -> createExpensiveGraphAsync(key, executor));

// 如果缓存中不存在,则查找并异步计算条目
CompletableFuture<Graph> graph = cache.get(key);
//查找并异步计算不存在的条目
CompletableFuture<Map<Key, Graph>> graphs = cache.getAll(keys);
```

AsyncLoadingCache是一个LoadingCache变体，用于计算Executor上的条目并返回CompletableFuture。 这是允许使用流行的响应式编程模型的缓存。

当计算以同步方式表示更好时，应提供CacheLoader。 当计算异步表示更好时，应该提供一个AsyncCacheLoader，并返回一个CompletableFuture。

synchronous()提供了一个阻塞直到异步计算完成的视图.

默认执行程序是ForkJoinPool.commonPool()，可以通过Caffeine.executor(Executor)覆盖。
