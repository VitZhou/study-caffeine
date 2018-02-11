# 清理

Caffeine缓存不会自动或在value过期后执行清理和驱逐value。相反,如果写操作很少,则在写操作之后或者读操作之后偶尔执行少量的维护。这个维护会委托给后台的Excutor,默认情况下ForkJoinPool.commonPool()，可以通过Caffeine.executor(Executor)覆盖。

原因如下：如果我们想持续缓存维护,我们需要在每个操作上锁定数据结构或者创建一个线程。一些环境限制了线程的创建，这会使Caffeine无法使用。
相反，我们把这个选择放在你的手中。如果您的缓存是高吞吐量的，那么您不必担心执行缓存维护来清理过期的条目等。如果您的缓存读取和写入很少，您可能希望创建自己的维护线程，定期调用cache.cleanUp()。
如果您想要定期对缓存进行常规缓存维护，只需很少的操作，例如使用ScheduledExecutorService安排维护。
