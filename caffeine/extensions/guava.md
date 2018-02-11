# Guava

```java
// Guava's LoadingCache interface
LoadingCache<Key, Graph> graphs = CaffeinatedGuava.build(
    Caffeine.newBuilder().maximumSize(10_000),
    new CacheLoader<Key, Graph>() { // Guava's CacheLoader
        @Override public Graph load(Key key) throws Exception {
          return createExpensiveGraph(key);
        }
    });
```

## Api兼容性

Caffeine提供了一个适配器去暴露guava接口的使用。 这些适配器提供了与下面的实施注意事项相同的API契约。 在可能的情况下，Guava喜欢的行为可以通过Guava的测试套件mock和验证。

当转换到Caffeine的接口，请注意，虽然两个缓存有相似的方法名称，行为可能会有所不同。 请咨询JavaDoc以更彻底地比较用法。

## 最大size(或权重)

Guava将在使用LRU算法达到最大size之前逐出。 Caffeine使用Window TinyLFU算法，一旦超过阈值，就会被驱逐出去。

## 即时到期

Guava为了立即到期(expireAfterAccess(0, timeUnit) and expireAfterWrite(0, timeUnit)),转换为将最大size设置为0。这会导致移除通知其移除的原因是因为size而不是因为到期.Caffeine可以正确的识别移除原因.

## 替换通知

Guava会在任何更新条目时通知移除监听器.Caffeine在引用等于以前的值和替换值时不会发出通知.

## invalidation并发

Guava会在invalidation时会忽略条目，但是这些条目仍在计算中.Caffeine在每个条目被invalidation时将阻塞调用者,直到它完成计算,然后将被删除. 但是,计算条目可能被invalidateAll()忽略,因为这些key被底层散列表所限制.如果使用异步缓存,则invalidation是非阻塞的,因为不完整的future将被删除,并且移除通知被委托给计算线程。

## 异步维护

在写入和读取操作期间,Guava将在调用现场上分摊维护工作.Caffeine将此定期维护委托给配置的Excutor(默认ForkJoinPool.commonPool()).在调用现场上调用一个显示的cleanUp();

## 异步通知

Guava可以从一个队列中使用任何调用现场处理移除通知。 Caffeine委托给配置的Excutor（默认：ForkJoinPool.commonPool（））。

## 异步刷新

Guava在请求刷新的线程上重新计算一个条目.Caffeine委托给配置的Excutor(默认:ForkJoinPool.commonPool()).

## 计算null值

Guava会在计算出NULL值时抛出异常,如果计算是刷新引起的保留原来条目.Caffeine返回Null值,并且如果计算是刷新引起的会删除条目.如果使用Guava试用期,用Guava CacheLoader构建Caffeine，Caffeine的行为就会跟Guava一样。

## 计算Map

21.0之前的Guava版本继承了非原子的ConcurrentMap默认方法（compute，computeIfAbsent，computeIfPresent和merge）。 Caffeine实现这些Java 8的方法并添加的原子版本。

## CacheStats

Guava的CacheStats.loadExceptionCount（）和CacheStats.loadExceptionRate（）分别在Caffeine中重命名为CacheStats.loadFailureCount（）和CacheStats.loadFailureRate（）。 这种变化是由于空值计算值被视为加载失败而不是例外。

## Android和GWT兼容性

由于不支持Java 8的平台，Caffeine不提供兼容性。