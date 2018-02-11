# 刷新

```java
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .maximumSize(10_000)
    .refreshAfterWrite(1, TimeUnit.MINUTES)
    .build(key -> createExpensiveGraph(key));
```

刷新跟驱逐不同,例如LoadingCache.refresh(K)会为key异步的加载新的值(如果有旧值那么刷新key的时候任然会被返回,但是检索会异步的等待直到重新加载该值).

与expireAfterWrite不同的是，refreshAfterWrite将在指定的持续时间之后使key符合刷新的条件，但刷新只会在查询条目时才会触发。因此，您可以在同一个缓存中同时指定refreshAfterWrite和expireAfterWrite，那么只要条目有资格进行刷新，就不会盲目重置。 如果条目在刷新后没有被查询，则允许到期。

CacheLoader可以通过覆盖CacheLoader.reload(K，V)来指定在刷新行为，这允许您在计算新值时使用旧值。

刷新操作是使用Executor异步执行的。 默认执行程序是ForkJoinPool.commonPool()，可以通过Caffeine.executor（Executor）覆盖。

如果刷新时引发异常，则保留旧值并记录异常（使用logger）并吞掉。
