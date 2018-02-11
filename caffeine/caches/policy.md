# 策略

缓存支持的策略在构建时是固定的.在运行时，可以检查和调整该配置。 这些策略是通过一个Optional来获得的，以指示缓存是否支持该功能。

## 基于size

```java
cache.policy().eviction().ifPresent(eviction -> {
  eviction.setMaximum(2 * eviction.getMaximum());
});
```

如果缓存受最大权重限制，则可以使用weightedSize()来获取当前权重。 这与报告存在条目数的Cache.estimatedSize()不同。
最大size或权重可以从getMaximum()或取，并使用setMaximum(long)进行调整。 调整后缓存将被驱逐，直到它在新的阈值内。
如果需要最有可能保留或驱逐的条目的子集，则hottest(int)和clodest(int)方法提供条目的有序快照。

## 基于时间

```java
cache.policy().expireAfterAccess().ifPresent(expiration -> ...);
cache.policy().expireAfterWrite().ifPresent(expiration -> ...);
cache.policy().expireVariably().ifPresent(expiration -> ...);
cache.policy().refreshAfterWrite().ifPresent(expiration -> ...);
```

ageOf(key，TimeUnit)提供了从expireAfterAccess，expireAfterWrite或refreshAfterWrite策略的角度来看条目已经空闲的时间。 最大持续时间可以从getExpiresAfter(TimeUnit)获取，并使用setExpiresAfter（long，TimeUnit）进行调整。

如果需要最有可能保留或过期的条目的子集，youngest(int)和oldest(int)方法提供条目的有序快照。