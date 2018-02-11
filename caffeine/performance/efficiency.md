# 效率

LRU算法由于其简单和良好的命中率在常见的情况下，是比较流行的一种驱逐策略。但是在负载较大时它不是最好的选择，而且在全局扫描等情况下可能具有较差的命中率.下面是经过广泛苹果后比较好的选择。为简单起见,只讨论执行O(1)时间负责度的驱逐策略的前三。这里不包含基于时间的策略.

caffeine需要高命中和低内存占用,选择了Window TinyLfu策略。

## Adaptive Replacement Cache(自适应缓存替换)

[ARC](https://github.com/ben-manes/caffeine/blob/master/simulator/src/main/java/com/github/benmanes/caffeine/cache/simulator/policy/adaptive/ArcPolicy.java)对一次命中的条目使用一个队列,对多次命中的条目使用另外一个队列,对正在监视的驱逐条目使用非驻留队列.根据缓存的中作负载模式和有效性动态调整队列的最大size.

该策略实施起来简单,但要求缓存大小加倍以保留驱逐的key。它也是获得专利的，不能再没有IBM许可协议的情况下使用。
//TBD 后面的内容太多专业术语···有时间再来