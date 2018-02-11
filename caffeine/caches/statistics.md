# 统计

```java
Cache<Key, Graph> graphs = Caffeine.newBuilder()
    .maximumSize(10_000)
    .recordStats()
    .build();
```

通过使用Caffeine.recordStats()，你可以打开统计信息收集。Cache.stats()方法返回提供统计信息的CacheStats，如:
- hitRate(): 返回请求命中率
- evictionCount(): 缓存逐出成员的数量
- averageLoadPenalty(): 加载新值所花费的平均时间

这些统计数据对缓存调整至关重要，我们建议在性能关键型应用程序中关注这些统计数据。

缓存统计信息可以使用基于pull或基于push的方法与报告系统集成。 基于pull的方法定期调用Cache.stats()并记录最新的快照。 基于push的方法提供自定义StatsCounter，以便在缓存操作期间直接更新指标。

有关使用[Dropwizard Metrics](http://metrics.dropwizard.io/)的简单示例，请参阅[stats-metrics](https://github.com/ben-manes/caffeine/tree/master/examples/stats-metrics)。

如果使用[Prometheus](https://prometheus.io/)，可以查看[simpleclient-caffeine](https://github.com/prometheus/client_java#caches)。

你还可以使用[Micrometer](http://micrometer.io/)集成.