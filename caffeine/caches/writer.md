# Writer

```java
LoadingCache<Key, Graph> graphs = Caffeine.newBuilder()
  .writer(new CacheWriter<Key, Graph>() {
    @Override public void write(Key key, Graph graph) {
      // write to storage or secondary cache
    }
    @Override public void delete(Key key, Graph graph, RemovalCause cause) {
      // delete from storage or secondary cache
    }
  })
  .build(key -> createExpensiveGraph(key));
```

CacheWriter允许缓存充当底层资源的门面(开闭原则)，当与CacheLoader结合使用时，所有的读写操作都可以通过缓存进行传播。Writer将缓存中的原子操作扩展为同步加载外部资源。这意味着缓存将阻止对条目的后续变更操作,并且读操作会返回先前的旧值,直到写入的完成.如果写入程序失败,那么银蛇保持不变.抛出的异常会传递给调用者。

CacheWriter会在创建，变更或删除条目时得到通知。加载(例如，LoadingCache.get)，重新加载(例如，LoadingCache.refresh)或计算(例如Map.computeIfPresent)将不被告知。

请注意，CacheWriter不能与弱引用key或AsyncLoadingCache结合使用。

## 可能的用例

CacheWriter是复杂工作流的扩展点，需要外部资源来观察给定键的变化顺序。 这些用法是Caffeine支持，但不是本地内置。

### write模式

一个CacheWriter可能被用来实现一个write-through或write-back缓存。

在write-through(直接写)模式高速缓存中，操作是同步执行的，只有在Writer成功完成后才会更新高速缓存。 这样可以避免资源和缓存分别作为独立的原子操作进行更新的争用情况。

在write-back(回写)式高速缓存中，在高速缓存更新之后，对外部资源的操作是异步执行的。 这可以提高写入吞吐量，避免数据不一致的风险，例如写入失败时在缓存中保留无效状态。 这种方法可能有助于延迟写入(直到特定时间)，限制写入速率或批量写入操作。

write-back回写式扩展可能实现以下部分或全部功能：

- 批处理和合并操作
- 在时间窗口内延迟操作
- 如果超过阈值大小，则在定期刷新之前执行批处理
- 如果操作尚未刷新，则从write-behind缓冲加载
- 根据外部资源的特点，处理重审，速率限制和并发

有关使用RxJava的简单示例，请参阅[write-behind-rxjava](https://github.com/ben-manes/caffeine/tree/master/examples/write-behind-rxjava)。

### 分层

一个CacheWriter可能被用来集成多个缓存层。

分层缓存加载和写入由system record的外部缓存支持。

允许有一个小的快速缓存，回落(falls back)慢的大缓存。 典型的层次是堆外，基于文件和远程缓存。

victim缓存是一个分层变体，其中被逐出的条目被写入二级缓存。 delete(K，V，RemovalCause)允许检查为什么该条目被删除，并作出相应的反应。

### 同步监听

CacheWriter可用于发布到同步侦听器。

同步侦听器按照给定key在缓存上的操作顺序接收事件通知。 监听器可以阻止缓存操作，也可以将事件排队以异步执行。 这种类型的侦听器最常用于复制或构建分布式缓存。


