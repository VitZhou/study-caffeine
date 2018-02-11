# 驱逐(Eviction)

Caffeine提供三种类型的驱逐：基于size的驱逐，基于时间的驱逐和基于引用的驱逐。

## 基于size

```java
//基于高速缓存中条目的数量进行驱逐
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .maximumSize(10_000)
    .build(key -> createExpensiveGraph(key));

// 基于高速缓存中vertices的数量进行回退
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .maximumWeight(10_000)
    .weigher((Key key, Graph graph) -> graph.vertices().size())
    .build(key -> createExpensiveGraph(key));
```

如果您的缓存不应超过一定的大小，请使用Caffeine.maximumSize(long)。 缓存将试图驱逐最近或最经常使用的条目。

或者，如果不同的缓存条目具有不同的“权重”（例如，如果缓存value具有完全不同的内存占用情况），则可以使用Caffeine.weigher(Weigher)指定权重函数，并使用Caffeine.maximumWeight(long)。 除了与maximumSize要求相同的警告外，请注意，需要在创建条目和更新时计算权重，此后是静态的，并且在进行驱逐选择时不使用相对权重。

## 基于时间

```java
// 基于固定的到期策略进行退出
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .expireAfterAccess(5, TimeUnit.MINUTES)
    .build(key -> createExpensiveGraph(key));
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .expireAfterWrite(10, TimeUnit.MINUTES)
    .build(key -> createExpensiveGraph(key));

// 基于不同的到期策略进行退出
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .expireAfter(new Expiry<Key, Graph>() {
      public long expireAfterCreate(Key key, Graph graph, long currentTime) {
        // 如果来自外部资源，则使用挂钟时间，而不是使用纳米时间
        long seconds = graph.creationDate().plusHours(5)
            .minus(System.currentTimeMillis(), MILLIS)
            .toEpochSecond();
        return TimeUnit.SECONDS.toNanos(seconds);
      }
      public long expireAfterUpdate(Key key, Graph graph,
          long currentTime, long currentDuration) {
        return currentDuration;
      }
      public long expireAfterRead(Key key, Graph graph,
          long currentTime, long currentDuration) {
        return currentDuration;
      }
    })
    .build(key -> createExpensiveGraph(key));
```

Caffeine提供三种方法来定时驱逐:
- expireAfterAccess(long, TimeUnit): 自条目最后一次读取或写入以来经过指定的时间后，将条目过期。 适用于如存的数据绑定到会话并由于不活动而过期的场景。
- expireAfterWrite(long, TimeUnit): 自创建条目以来或最近一次的值替换经过了指定的时间长度之后的到期条目。 适用于缓存的数据在一段时间后延长时间的场景。
- expireAfter(Expiry): 在变量持续时间过后，将条目过期。 当条目的到期时间由外部资源确定时，适合这种方式。

在写入期间定期进行维护，偶尔在读取期间执行到期。 计划和触发到期事件是在O(1)时间内执行的。

测试定时驱逐不要求测试等到挂钟时间结束。 使用Ticker接口和Caffeine.ticker（Ticker）方法在缓存生成器中指定时间源，而不必等待系统时钟。

## 基于引用

```java
// 在没有key和值都无法到达的情况下退出
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .weakKeys()
    .weakValues()
    .build(key -> createExpensiveGraph(key));

// 当垃圾收集器需要释放内存时退出
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
    .softValues()
    .build(key -> createExpensiveGraph(key));
```

Caffeine允许你对键或值使用弱引用，以及对值使用软引用来设置你的缓存以便允许gc回收条目。请注意，AsyncLoadingCache不支持弱和软引用值。

Caffeine.weakKeys()使用弱引用存储key。这允许条目被垃圾收集，如果没有其他强引用的key。由于垃圾收集只依赖于identity是否相等，因此这会导致整个缓存使用identity（==）相等来比较key，而不是使用equals()。

Caffeine.weakValues()使用弱引用存储值。这允许条目被垃圾回收，如果没有其他强引用的直。由于垃圾收集只依赖于identity是否相等，因此这会导致整个缓存使用identity（==）相等来比较key，而不是使用equals()。

Caffeine.softValues()使用软引用存储值。为了响应内存需求，软引用的对象以全局最近最少使用的方式进行垃圾收集。由于使用软引用的性能影响，我们通常建议使用更可预测的基于size的最大高速缓存size。 softValues()的使用将使得使用identity（==）相等而不是equals（）来比较值。

