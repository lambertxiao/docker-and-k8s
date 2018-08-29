# Hello Kubernetes

## 安装minikube

* 下载阿里云版本的minikube
curl -Lo minikube http://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/releases/v0.25.2/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/

* 安装kvm2
    * sudo apt install libvirt-bin qemu-kvm
    * sudo usermod -a -G libvirtd $(whoami)
    * newgrp libvirtd
    * curl -Lo docker-machine-driver-kvm2 https://storage.googleapis.com/minikube/releases/latest/docker-machine-driver-kvm2 \
    && chmod +x docker-machine-driver-kvm2 \
    && sudo cp docker-machine-driver-kvm2 /usr/local/bin/ \
    && rm docker-machine-driver-kvm2


* 开启minikube
    minikube start --vm-driver kvm2


## 编写Hello Kubernetes

* 创建一个server.js

    ```
    var http = require('http');

    var handleRequest = function(request, response) {
    console.log('Received request for URL: ' + request.url);
    response.writeHead(200);
    response.end('Hello Kubernetes!');
    };

    var www = http.createServer(handleRequest);

    www.listen(8080);

    ```

* 编写Dockerfile

    ```
    FROM node:6.9.2
    EXPOSE 8080
    COPY server.js .
    CMD node server.js
    ```

* 确保了使用minikube虚拟驱动

    eval $(minikube docker-env)

* 构建镜像

    docker build -t hello-node:v1 .

* 将镜像推送到aliyun的容器服务

    https://cr.console.aliyun.com/cn-hangzhou/repositories

* 使用Deployment来创建Pod资源

    kubectl run hello-node --image=registry.cn-hangzhou.aliyuncs.com/lambertxiao/hello-kubernetes:v1 --port=8080

* 创建Service,暴露Deployment

    kubectl expose deployment hello-node --type=LoadBalancer

* 启动Service

    minikube service hello-node
