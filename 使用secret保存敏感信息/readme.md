## Secrets概览

> Secrets是用来保存敏感数据(密码, token等)的对象,减少意外暴露的风险,通过配置在volumes中,从而能够在container里获取secret的数据

## 从文件创建一个Secrets

```
kubectl create secret generic db-user-pass --from-file=username.txt,password.txt
```

## 通过配置文件创建Secrets

```
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
  password: MWYyZDFlMmU2N2Rm
```

## 创建Pod, 并通过volume使用Secrets

```
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: mysecret
```

在 `spec.volumes[].secret` 中声明使用的secret的名字,在 `spec.containers[].volumeMounts[]` 中使用对应的volume,并指定mountPath, 然后每一个在secret data里的键值对,key会变成文件的名字,value是文件内容,所有这些文件都挂载在mountPath下

比如上面的secret使用后,会在redis容器里的 `/etc/foo` 目录下生成username文件和password文件,文件的内容就是对应的值

这里生成文件的名称默认用的时data键值对的键名,也可以自己指定

```
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: mysecret
      items:
      - key: username
        path: my-group/my-username
```

此时在 `/etc/foo` 目录下面生成的是 `/etc/foo/my-group/my-username` 文件,而不是 `/etc/foo/username`,注意,这里的items里没有指定password,因此password没有被映射到 `/etc/foo` 目录下

**secret生成的文件的权限是可以修改的,默认的权限是644**

## 创建Pod并通过环境变量读取Secrets

```
apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
spec:
  containers:
  - name: mycontainer
    image: redis
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: username
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: password
  restartPolicy: Never
```
