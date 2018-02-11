# Home

Caffeine是一个高性能的基于jdk8的缓存库,提供了接近最佳的命中率。

缓存也ConcurrentMap类似,但是并不完全相同.最重要的区别是:1.ConcurrentMap会持久化它所包含的所有的元素,直到它们被明确的删除.2.高速缓存一般配置为自动删除元素，以限制其内存占用.在某些情况下,由于LoadCache或AsyncLoadingCache可以自动缓存加载所以即使它不能驱逐条目也可以使用。

Caffeine提供了灵活的结构用于创建缓存和以下功能组合:
- [自动加载条目](https://github.com/ben-manes/caffeine/wiki/Population)到缓存中,提供可选异步
- [基于size的驱逐](https://github.com/ben-manes/caffeine/wiki/Eviction#size-based): 当超过最大值时会驱逐使用频率最高或最近一次使用的条目
- [基于时间的驱逐](https://github.com/ben-manes/caffeine/wiki/Eviction#time-based): 通过上次访问或者上次写入的时间来测量
- [异步刷新](https://github.com/ben-manes/caffeine/wiki/Refresh): 当发生第一个过时请求时进行异步刷新.
- key自动包装在[弱引用](https://github.com/ben-manes/caffeine/wiki/Eviction#reference-based)中
- value自动包装在[弱或软引用](https://github.com/ben-manes/caffeine/wiki/Eviction#reference-based)中
- [通知](https://github.com/ben-manes/caffeine/wiki/Removal)被驱逐(或其他方式删除)的条目
- [统计](https://github.com/ben-manes/caffeine/wiki/Statistics)缓存的访问
- 写操作可以通过外部资源传播

为了方便集成，在扩展模块中提供了JSR-107 JCache和Guava适配器。 JSR-107标准化了基于Java 6的API，以特性和性能为代价来最小vendor的特定代码。 Guava的cache是其predecessor库，适配器提供了一个简单的迁移策略。