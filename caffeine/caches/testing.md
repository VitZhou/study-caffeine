# 测试

```java
FakeTicker ticker = new FakeTicker(); // Guava's testlib
Cache<Key, Graph> cache = Caffeine.newBuilder()
    .expireAfterWrite(10, TimeUnit.MINUTES)
    .executor(Runnable::run)
    .ticker(ticker::read)
    .maximumSize(10)
    .build();

cache.put(key, graph);
ticker.advance(30, TimeUnit.MINUTES)
assertThat(cache.getIfPresent(key), is(nullValue());
```

测试定时驱逐不要求测试等到挂钟时间结束。 使用Ticker接口和Caffeine.ticker（Ticker）方法在缓存builder中指定时间源，而不必等待系统时钟。 Guava的testlib为此提供了一个方便的FakeTicker。
Caffeine将定期维护，移除通知和异步计算委托给Executor。 这通过默认使用ForkJoinPool.commonPool()来提供更可预测的响应时间。 使用Caffeine.executor（Executor）方法在缓存构建器中指定直接（相同的线程）执行程序，而不必等待异步任务完成。
我们推荐使用[Awaitility](https://github.com/awaitility/awaitility)进行多线程测试。

