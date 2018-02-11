# 模拟器

模拟器包括一系列驱逐策略和distribution generators。 这有助于调查策略是否适合使用场景。

## 用法

指定所需的[配置](https://github.com/ben-manes/caffeine/blob/master/simulator/src/main/resources/reference.conf)后，在IDE中运行[模拟器](https://github.com/ben-manes/caffeine/blob/master/simulator/src/main/java/com/github/benmanes/caffeine/cache/simulator/Simulator.java)。或者，在命令行中运行gradlew simulator:run。 以下跟踪格式受支持。

| 仓库 | 大小 | Location	|
|--------|--------|--------|
|   LIRS     |    small    |	[git repository](https://github.com/ben-manes/caffeine/tree/master/simulator/src/main/resources/com/github/benmanes/caffeine/cache/simulator/parser/lirs) |
|   UCSD     |    small    |	[git repository](https://github.com/ben-manes/caffeine/tree/master/simulator/src/main/resources/com/github/benmanes/caffeine/cache/simulator/parser/address) |
|   ARC     |    large    |	[author's homepage](https://github.com/ben-manes/caffeine/tree/master/simulator/src/main/resources/com/github/benmanes/caffeine/cache/simulator/parser/address) |
|   UMass     |    large    |	[UMass Storage](http://traces.cs.umass.edu/index.php/Storage/Storage) |
|   WikiBench     |    large    |	[WikiBench](http://www.wikibench.eu/) |

## 报告示例

由于批量和广播，在独立运行每个策略时，时间是可比的。

| 策略 | 命中率 | 命中次数(hits)	| 未命中次数(misses)	| 请求次数(requests)	| 驱逐数量(Evictions)	| Steps|总共耗时(Time)|
|--------|--------|--------|--------|--------|--------|--------|--------|--------|
|opt.Clairvoyant 	    |58.79%	|9,323	|6,535	|15,858	|6,035	|?	            |278.0ms    |
|sketch.WindowTinyLfu	|56.11%	|8,898	|6,960	|15,858	|6,460	|15,858 (100 %)	|315.2 ms	|
|irr.Lirs	            |55.97%	|8,876	|6,982	|15,858	|6,482	|27,689 (174 %)	|311.0 ms	|
|adaptive.Arc	        |49.39%	|7,833	|8,025	|15,858	|7,525	|15,858 (100 %)	|166.3 ms	|
|linked.Lru	            |46.51%	|7,375	|8,483	|15,858	|7,983	|15,858 (100 %)	|128.2 ms	|
|linked.Fifo	        |40.72%	|6,457	|9,401	|15,858	|8,901	|15,858 (100 %)	|163.6 ms 	|
