# ConcurrentLinkedHashMap

## 计算

[ConcurrentLinkedHashMap](https://code.google.com/p/concurrentlinkedhashmap)继承了非原子的ConcurrentMap默认方法（compute，computeIfAbsent，computeIfPresent和merge）。 Caffeine实现这些Java 8方法并添加的原子版本。

## 权重

就像Guava一样ConcurrentLinkedHashMap要求最小权重为1，Caffeine允许最小权重为0，以表示基于size的策略时不会被驱逐。

## 异步通知

ConcurrentLinkedHashMap可能从调用线程队列中的任何线程处理驱逐通知。 Caffeine委托给配置的执行器（默认：ForkJoinPool.commonPool（））。

## 快照视图

ConcurrentLinkedHashMap以保留顺序支持快照视图。 Caffeine在Policy.Eviction中提供了此功能，通过Cache.policy（）获取，其中ascendingMapWithLimit按最冷升序，descendingMapWithLimit按最热降序。

## 序列化

ConcurrentLinkedHashMap在序列化时保留条目并丢弃驱逐顺序。 Caffeine像Guava一样，只保留配置并没有数据。