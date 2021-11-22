# EMIT语句

EMIT语句可以使QUERY根据不同场景，定义不同的输出策略，从而达到控制延迟或提高数据准确性的效果。



## 使用限制

* EMIT策略只支持tumble和hop窗口，暂不支持session窗口
* 如果一个job有多个输出，则多个输出的emit需要定义相同策略，后续会支持不同策略
* emit语法还不能用来配置minibatch的allowLateness



## 什么是EMIT策略

emit策略是指在Flinlk SQL中，query根据不同场景选择不同的输出策略（例如最大延迟时长）。1小时的时间窗口，窗口触发之前希望每分钟都能看到最新的结果，窗口触发之后希望不丢失迟到一天的数据。如果1小时窗口内的统计结果无变化，则不更新输出结果；如果1小时窗口内的统计结果有变化，则更新输出结果。

* 窗口结束之前，按1分钟延迟输出，窗口结束之后无延迟输出

```sql
emit
	with delay '1' minute before watermark,
	without delay after watermark
```

* 窗口结束之前不输出，窗口结束之后无延迟输出

```sql
emit without delay after watermark
```

* 全局都按1分钟的延迟输出

```sql
emit with delay '1' minute
```

* 窗口结束之前都按1分钟延迟输出。

```sql
emit
	with delay '1' minute before watermark
```



























