---
keyword: [安全沙箱Pod内核参数, 特权容器, Annotation]
---

# 配置安全沙箱Pod内核参数

通常容器提供的默认内核参数配置能够满足大部分业务的日常需求，但在某些特殊的业务场景中为了让应用达到最佳的运行状态，往往需要优化部分内核参数配置。安全沙箱支持通过Annotation为Pod配置自定义内核参数，也支持开启特权容器模式后在Pod内通过Sysctl设置内核参数。本文主要介绍如何为安全沙箱Pod配置内核参数。

-   [创建安全沙箱托管版集群](/cn.zh-CN/Kubernetes集群用户指南/安全沙箱/创建安全沙箱集群/创建安全沙箱托管版集群.md)。
-   [通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。

在Kubernetes中可通过Pod的SecurityContext和Annotation来配置命名空间的安全和非安全Sysctl参数，除此之外还可以启动特权容器在容器内配置非命名空间的Sysctl参数。

由于runC Pod与主机共享内核，存在以下问题：

-   虽然特权容器几乎拥有与主机进程一样的访问权限，但在修改Sysctl参数或非安全的Sysctl参数时，可能会影响其他Pod或者导致节点不可用等问题。
-   无法为同一节点上的不同Pod分别定制非命名空间的内核配置。

## 安全沙箱核心特点

安全沙箱Pod拥有独享内核。通过Annotation为Pod配置自定义内核参数或开启特权容器模式，可以解决runC Pod由于共享内核存在的问题：

-   通过Annotation可以为Pod配置所有合法的Sysctl参数，包括命名空间和非命名空间Sysctl参数，这些配置仅对Pod所在的内核有效，不会影响其他Pod和宿主机的Sysctl配置。
-   通过开启特权容器模式，可以使容器几乎享有沙箱内核的所有访问权限。由于沙箱独享内核，容器的访问权限仅限于沙箱内部，不会对其他Pod和宿主机的内核有影响。

## 通过Pod Annotation定制Sysctl参数

通过`Annotation securecontainer.alibabacloud.com/sysctls`配置Pod的Sysctl参数，取值可以是一个或多个Sysctl参数，多个Sysctl参数用逗号分隔。

比如当需要开启网络包转发功能时，可通过以下Annotation配置Sysctl参数：

```
annotations:
  securecontainer.alibabacloud.com/sysctls: "net.bridge.bridge-nf-call-ip6tables=1,net.bridge.bridge-nf-call-iptables=1,net.ipv4.ip_forward=1"
```

完整的YAML示例如下：

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: busybox
  name: busybox
  namespace: default
spec:
  replicas: 2
  selector:
    matchLabels:
      app: busybox
  template:
    metadata:
      labels:
        app: busybox
      annotations:
        securecontainer.alibabacloud.com/sysctls: "net.bridge.bridge-nf-call-ip6tables=1,net.bridge.bridge-nf-call-iptables=1,net.ipv4.ip_forward=1"
    spec:
      containers:
        - command:
            - sleep
            - infinity
          image: 'busybox:latest'
          imagePullPolicy: IfNotPresent
          name: busybox
          resources:
            limits:
              cpu: '1'
              memory: 1Gi
            requests:
              cpu: 500m
              memory: 512Mi
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      runtimeClassName: runv
```

## 配置特权容器

安全沙箱Pod和runC Pod配置特权容器的方式相同。他们的区别在于runC Pod配置的是主机的内核，安全沙箱Pod配置的是沙箱的内核。

可通过以下命令配置特权容器：

```
containers:
  - name: busybox
    securityContext:
      privileged: true
```

完整的YAML示例如下：

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: busybox
  name: busybox
  namespace: default
spec:
  replicas: 2
  selector:
    matchLabels:
      app: busybox
  template:
    metadata:
      labels:
        app: busybox
    spec:
      containers:
        - command:
            - sh
            - -c 
            - "sysctl -w fs.inotify.max_user_watches=524286 && tail -f /dev/null"
          image: 'busybox:latest'
          imagePullPolicy: IfNotPresent
          name: busybox
          securityContext:
            privileged: true
          resources:
            limits:
              cpu: '1'
              memory: 1Gi
            requests:
              cpu: 500m
              memory: 512Mi
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      runtimeClassName: runv
```

[创建安全沙箱托管版集群](/cn.zh-CN/Kubernetes集群用户指南/安全沙箱/创建安全沙箱集群/创建安全沙箱托管版集群.md)

[runC容器和安全沙箱（runV）容器的区别](/cn.zh-CN/Kubernetes集群用户指南/安全沙箱/runC容器和安全沙箱（runV）容器的区别.md)

[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)

