## Kubernetes的知识

## Kubernetes提供了什么

假设你的团队部署了一个普通的php应用,并且需要在任何时间内都运行着5个应用实例,当没有Kubernetes的时候,可能会有如下情况发生

> 1. 某个应用实例不可用了(宕机等情况)
> 2. 需要团队有监控实例运行的设置,然后通过某个人说应用挂了
> 3. 某人接到挂了的通知,需要研究宕机原因,并手动重启应用
> 4. 新启动应用的网络IP需要更新到负载均衡机器那边

当有了Kubernetes之后,整个流程都可以交给它来完成.

Kubernetes需要知道您希望什么状态的集群,可以根据yaml等配置文件声明需要某个对象的状态,当我们写完某个配置文件之后,都是交给 `kube-apiserver` 处理的.

## Controller

当你通过Kubenetes API声明了你所期望的资源状态之后,controller的作用就是让当前集群的状态与你所期望的状态匹配起来,标准的controller进程有 `kube-controller-manager` 和 `cloud-controller-manager`, 它们都遵循下面的准则

> 1. 当前集群的状态(X)是什么
> 2. 期望的状态(Y)是什么
> 3. X == Y 什么也不干,两个不等时,将X的状态逐渐变为Y
