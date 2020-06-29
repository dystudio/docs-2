# Sqldiag<a name="ZH-CN_TOPIC_0253059671"></a>

Sqliag是openGauss中SQL语句执行时长预测框架。现有的预测技术主要基于执行计划的模型预测，但这些预测方案仅适用于OLAP场景且可以获取执行计划的作业，对于OLTP或者HTAP这样的快速查询是没有使用价值的。和上述方案不同，Sqldiag着眼于数据库的历史SQL语句，因为短时间内数据库SQL语句执行时长不会有太大的差距，Sqldiag可以从历史数据中检测出已输入指令相似的指令集，并基于SQL向量化技术和时序预测算法预测SQL语句执行时长。该框架有如下优点：

1.  不需要指令的执行计划，对数据库性能不会有任何的影响。
2.  使用场景广泛，目前业内的很多算法具有很强的针对性，比如只适用于OLTP或者OLAP，Sqldiag使用场景广泛。
3.  该框架鲁棒性强，理解容易，只需要简单的修改，用户就可以设计出自己的预测模型。

-   **[概述](概述-11.md)**  

-   **[使用指导](使用指导-12.md)**  

-   **[获取帮助](获取帮助-13.md)**  

-   **[命令参考](命令参考-14.md)**  

-   **[常见问题处理](常见问题处理-15.md)**  

