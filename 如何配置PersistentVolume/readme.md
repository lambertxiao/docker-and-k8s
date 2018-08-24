## PersistentVolume

## 在Node上创建一个index.html

* mkdir /home/yt00707/mnt/data
* echo 'Hello from Kubernetes storage' > /home/yt00707/mnt/data/index.html

## 2. 配置PersistentVolume

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: task-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/home/yt00707/mnt/data"
```

用来作为和PVC的匹配值, 值内容可以自定义

```
storageClassName: manual
```

设置数据卷的大小为10G

```
capacity:
  storage: 10Gi
```

设置可以被单个node挂载为读写

```
accessModes:
  - ReadWriteOnce
```

挂载节点上的某个目录到PV里

```
hostPath:
  path: "/mnt/data"
```

## 3. 定义PersistentVoumeClaim可以为多个PV所用

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: task-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```

在你创建完PVC之后,kubernetes会查找满足PVC要求的PV(通过storageClassName匹配),再为这些PV绑定上PVC

## 4. 创建Pod

```
apiVersion: v1
kind: Pod
metadata:
  name: task-pv-pod
spec:
  volumes:
  - name: task-pv-storage
    persistentVolumeClaim:
      claimName: task-pv-claim
  containers:
  - name: task-pv-container
    image: nginx
    ports:
    - containerPort: 80
      name: "http-server"
    volumeMounts:
    - mountPath: "/usr/share/nginx/html"
      name: task-pv-storage
```

声明volumes, 并使用前面创建的PVC

```
volumes:
- name: task-pv-storage
  persistentVolumeClaim:
    claimName: task-pv-claim
```

在container中设置数据卷的mount路径,将容器里文件的位置挂载到数据卷上

```
volumeMounts:
- mountPath: "/usr/share/nginx/html"
  name: task-pv-storage
```

## 5. 在Pod里通过curl请求localhost查看能否读到第一步创建的文件

请求不到,可能配置有误,在/usr/share/nginx/html目录下手动创建文件可以访问到

## 6. 访问控制

配置了Group ID的存储只允许被拥有同样GID的Pod使用

```
kind: PersistentVolume
apiVersion: v1
metadata:
  name: pv1
  annotations:
    pv.beta.kubernetes.io/gid: "1234"
```

GID写在annotations中,当Pod的定义中拥有相同的 `pv.beta.kubernetes.io/gid` 时,该Pod才能使用这个PV
