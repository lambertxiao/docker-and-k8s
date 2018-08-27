# Kubernetes的其他工作负载模式

可以了解到以下内容:

* 除了deployment之外的其他工作负载模式

## Persistent workloads

适合需要长久运行的应用程序

* StatefulSets

    相比Deployment, StatefulSets可以提供更强的"recovery"行为, 举个例子

    |　| Deployment | StatefulSet |
    | - | - | - |
    | Pod的名字 |　example-b1c3 | example-0
    | 当Pod死亡时　| 会重新调度到任何一个节点上面，并会有一个新的名称example-a51z | 重新在相同的节点创建一个Pod, 还是example-0
    | 当一个Node变得不可达时 | Pods会被调度到一个新的节点,拥有新的名称 | Pod会被标记为 "Unknown", 并且不会被重新调度,除非该节点被强制删除

    StatefulSets非常适合分布式数据存储，如Cassandra或Elasticsearch

* DaemonSets

    即使在添加或者交换节点时,DaemonSets也会在集群的每一个节点上连续运行,这个保证对于在集群中设置全局行为特别有效,例如:

    * 日志或监控程序,如 `fluentd`
    * 网络代理

## Terminating workloads

相比deployment,这是API对象是有限的,当Pod的任何执行完之后会自动停止

* Jobs

    可以将它们用于一次性任务，例如运行脚本或设置工作队列。这些任务可以顺序执行或并行执行

    一个Job会创建一个或多个Pods,当pods成功结束后,Job会追踪完成结果,当全部完成后,该Job的任务就结束了

* CronJobs

    Jobs类似，但允许您安排在特定时间执行或定期重复执行
