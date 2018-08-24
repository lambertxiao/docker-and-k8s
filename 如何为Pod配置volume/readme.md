## Volume

> Volume是Pod级别的数据卷,用来在一个Pod里为多个container提供数据卷挂载功能.其生命周期与container的生命周期无关,仅当Pod销毁时,volume才销毁

## 为Pod配置一个volume

创建一个运行一个容器的Pod,这个Pod有一个 `emptyDir` 类型的volume, 即使容器运行终止或重新启动,volume都会存在

```
apiVersion: v1
kind: Pod
metadata:
  name: redis
spec:
  containers:
  - name: redis
    image: redis
    volumeMounts:
    - name: redis-storage
      mountPath: /data/redis
  volumes:
  - name: redis-storage
    emptyDir: {}
```

通过volumes在Pod中声明数据卷

```
volumes:
- name: redis-storage
  emptyDir: {}
```

在Container中使用volumeMount挂载相应目录到volume上

```
volumeMounts:
- name: redis-storage
  mountPath: /data/redis
```

## 示例

* 创建Pod

    * kubectl create -f redis.yaml

* 监听Pod的运行

    * kubectl get pod redis --watch

* 在另外的terminal中,执行

    * kubectl exec -it redis -- /bin/bash

* 到/data/redis目录,创建一个文件

    * cd /data/redis/
    * echo Hello > test-file

* 安装procps来操作进程

    * apt-get update
    * apt-get install procps
    * ps aus

* 杀掉redis进程,会自动退出pod,返回前一个terminal会发现pod正在重新启动

    * kill pid

* 重新进入pod中,可以发现test-file文件还存在

    * kubectl exec -it redis -- /bin/bash

* 最后将Pod删除

    * kubectl delete pod redis
