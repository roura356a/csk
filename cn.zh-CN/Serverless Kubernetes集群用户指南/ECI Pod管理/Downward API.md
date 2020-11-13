# Downward API

容器服务Kubernetes版支持将Pod和Container字段呈现给运行中的容器。而呈现Pod和Container字段的方式称为Downward API。本文介绍如何将Pod和Container字段呈现给运行中的容器。

## 背景信息

容器服务Kubernetes版可以通过以下两种方式将Pod和Container字段呈现给运行中的容器。 这两种呈现Pod和Container字段的方式都称为Downward API，目前ACK、ASK和ECI支持Downward API的大部分常用字段，具体的使用文档见下文。

-   环境变量
-   DownwardAPIVolumeFile

## 环境变量

环境变量能通过`fieldRef`获得如下值：

-   `metadata.name` : Pod名称
-   `metadata.namespace` : Pod命名空间
-   `metadata.uid` : Pod的UID
-   `metadata.labels['<KEY>']` : 单个Pod标签值 `<KEY>` \(例如, `metadata.labels['mylabel']`\)
-   `metadata.annotations['<KEY>']` : 单个Pod的标注值 `<KEY>`。

除此之外，环境变量fieldRef还支持spec以及status字段：

-   spec.serviceAccountName

-   spec.nodeName

-   status.podIP

Deployment样例如下：

```
apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
kind: Deployment
metadata:
  name: vk-downward-env
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
        annotations: 
            regionId: cn-beijing
            platform: Aliyun ECI
        labels:
            app: nginx
            env: test
    spec:
      containers:
      - name: nginx
        image: nginx
        env:
        - name: MY_metadata.name
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: MY_metadata.namespace
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        - name: MY_metadata.uid
          valueFrom:
            fieldRef:
              fieldPath: metadata.uid
        - name: MY_metadata.labels
          valueFrom:
            fieldRef:
              fieldPath: metadata.labels['env']
        - name: MY_metadata.annotations
          valueFrom:
            fieldRef:
              fieldPath: metadata.annotations['regionId']
        - name: MY_status.podIP
          valueFrom:
            fieldRef:
              fieldPath: status.podIP
        - name: MY_spec.serviceAccountName
          valueFrom:
            fieldRef:
              fieldPath: spec.serviceAccountName
        - name: MY_spec.nodeName
          valueFrom:
            fieldRef:
              fieldPath: spec.nodeName
      nodeName: virtual-node-eci-0
```

登入容器查看环境变量，可以看到fieldRef都生效了：

```
root@default-vk-downward-env:/# env
MY_spec.nodeName=virtual-kubelet
MY_spec.serviceAccountName=default
MY_metadata.annotations=cn-beijing
MY_metadata.namespace=default
MY_metadata.uid=f4881309-f3dd-11e9-bcf9-9efaf54dcfa7
MY_metadata.name=vk-downward-env
MY_metadata.labels=test
MY_status.podIP=192.168.6.245
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_SERVICE_PORT=6443
PWD=/
PKG_RELEASE=1~buster
HOME=/root
KUBERNETES_PORT_443_TCP=tcp://172.22.128.1:443
NJS_VERSION=0.3.5
TERM=xterm
SHLVL=1
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_ADDR=172.22.128.1
KUBERNETES_SERVICE_HOST=192.168.6.120
KUBERNETES_PORT=tcp://172.22.128.1:443
KUBERNETES_PORT_443_TCP_PORT=443
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
NGINX_VERSION=1.17.4
_=/usr/bin/env
```

****

## DownwardAPIVolumeFile

DownwardAPIVolumeFile能通过`fieldRef`获得如下的值：

-   `metadata.name` ：Pod名称
-   `metadata.namespace` ： Pod命名空间
-   `metadata.uid` ：Pod的UID
-   `metadata.labels['<KEY>']` ：单个Pod标签值 `<KEY>` \(例如, `metadata.labels['mylabel']`\)
-   `metadata.annotations['<KEY>']` ：单个Pod的标注值 `<KEY>`

该部分和环境变量支持的范围及用法是一致的。除此之外，DownwardAPIVolumeFile的fieldRef还支持存储所有的labels以及annotations：

metadata.annotations：所有Pod的标签，以`label-key="label-value"`格式显示，每行显示一个label。

metadata.labels：Pod的注释，以`annotation-key="annotation-value"`格式显示，每行显示一个标签。

Deployment样例如下：

```
apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
kind: Deployment
metadata:
  name: vk-downward-down-volume
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
        annotations: 
            regionId: cn-beijing
            platform: Aliyun ECI
        labels:
            app: nginx
            env: test
    spec:
      containers:
      - name: nginx
        image: nginx
        volumeMounts:
        - name: podinfo
          mountPath: /etc/podinfo
          readOnly: false
      volumes:
      - name: podinfo
        downwardAPI:
          items:
            - path: "metadata.name"
              fieldRef:
                fieldPath: metadata.name
            - path: "metadata.namespace"
              fieldRef:
                fieldPath: metadata.namespace
            - path: "metadata.uid"
              fieldRef:
                fieldPath: metadata.uid
            - path: "metadata.labels"
              fieldRef:
                fieldPath: metadata.labels
            - path: "metadata.annotations"
              fieldRef:
                fieldPath: metadata.annotations
      nodeName: virtual-node-eci-0
```

登入容器查看Volume的挂载目录，可以看到Volume的fieldRef都生效了，并存储在了容器指定的目录下：

```
Welcome to Alibaba Cloud Elastic Container Instance!
root@default-vk-downward-down-volume:/# cd /etc/podinfo/
root@default-vk-downward-down-volume:/etc/podinfo# ls
metadata.annotations  metadata.labels  metadata.name  metadata.namespace  metadata.uid
root@default-vk-downward-down-volume:/etc/podinfo# cat metadata.namespace 
default
root@default-vk-downward-down-volume:/etc/podinfo# cat metadata.name
vk-downward-down-volume
root@default-vk-downward-down-volume:/etc/podinfo# cat metadata.uid 
fa50b2b2-f3e3-11e9-bcf9-9efaf54dcfa7
root@default-vk-downward-down-volume:/etc/podinfo# cat metadata.annotations 
platform="Aliyun ECI"
regionId="cn-beijing"
root@default-vk-downward-down-volume:/etc/podinfo# cat metadata.labels 
app="nginx"
env="test"
root@default-vk-downward-down-volume:/etc/podinfo# 
```

目前仅支持Pod字段，而容器的字段还不支持，例如：

```
limits.cpu
requests.cpu
limits.memory
requests.memory
limits.ephemeral-storage
requests.ephemeral-storage
```

