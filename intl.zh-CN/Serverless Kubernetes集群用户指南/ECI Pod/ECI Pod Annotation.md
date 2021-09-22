ECI Pod Annotation 
=======================================

Kubernetes集群通过虚拟节点创建Pod到ECI时，为充分使用ECI提供的功能，在不改变Kubernetes语义的前提下，您可以根据需求为Pod添加Annotation。 本文为您介绍ECI实例支持的Annotation及其配置示例。

ECI实例目前支持的Annotation如下表所示。
**说明**

下表列举的Annotation仅适用于创建到虚拟节点上的Pod，即ECI实例，调度到ECS上的Pod不受这些Annotation影响。


|                       参数                       |              示例值               |                                                                                            描述                                                                                             |                                    相关文档                                    |
|------------------------------------------------|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------|
| k8s.aliyun.com/eci-security-group              | sg-bp1dktddjsg5nktv\*\*\*\*    | 安全组ID。                                                                                                                                                                                    | [配置安全组](t1918283.html#topic-1918283)                       |
| k8s.aliyun.com/eci-vswitch                     | vsw-bp1xpiowfm5vo8o3c\*\*\*\*  | 交换机ID，支持指定多个交换机实现多可用区功能。                                                                                                                                                                  | [多可用区创建实例](t1876030.html#topic-1876030)                    |
| k8s.aliyun.com/eci-schedule-strategy           | VSwitchOrdered                 | 多可用区调度策略。取值范围： * VSwitchOrdered：按顺序   * VSwitchRandom：随机                               | [多可用区创建实例](t1876030.html#topic-1876030)                    |
| k8s.aliyun.com/eci-ram-role-name               | AliyunECIContainerGroupRole    | RAM角色，赋予ECI访问阿里云产品的能力。                                                                                                                                                                    | [授权RAM角色](t1860118.html#topic1525)                         |
| k8s.aliyun.com/eci-use-specs                   | 2-4Gi,4-8Gi,ecs.c6.xlarge      | ECI实例规格，支持指定多规格，包括指定vCPU和内存，或者ECS规格。                                                                                                                                                      | [多规格创建实例](t1860149.html#topic-1860149)                     |
| k8s.aliyun.com/eci-spot-strategy               |  SpotAsPriceGo | 抢占式实例策略。取值范围： * SpotAsPriceGo：系统自动出价，跟随当前市场实际价格。   * SpotWithPriceLimit：设置抢占实例价格上限。    | [创建抢占式实例](t1876161.html#topic-1876161)                     |
| k8s.aliyun.com/eci-spot-price-limit            | 0.5                            | 抢占式实例价格。仅当k8s.aliyun.com/eci-spot-strategy设置为SpotWithPriceLimit时有效。                                                                                                                       | [创建抢占式实例](t1876161.html#topic-1876161)                     |
| k8s.aliyun.com/eci-cpu-option-core             | 2                              | CPU物理核心数。                                                                                                                                                                                 | [自定义CPU选项](t2020903.html#topic-2020903)                    |
| k8s.aliyun.com/eci-cpu-option-ht               | 1                              | 每核线程数。                                                                                                                                                                                    | [自定义CPU选项](t2020903.html#topic-2020903)                    |
| k8s.aliyun.com/eci-reschedule-enable           | "true"                         | 是否开启ECI重调度。                                                                                                                                                                               | 无，详见下文。                                                                    |
| k8s.aliyun.com/pod-fail-on-create-err          | "true"                         | 创建失败的ECI实例是否体现Failed状态。                                                                                                                                                                   | 无，详见下文。                                                                    |
| k8s.aliyun.com/eci-image-snapshot-id           | imc-2zebxkiifuyzzlhl\*\*\*\*   | 指定镜像缓存ID。 **说明** 使用镜像缓存支持手动指定和自动匹配两种方式，建议使用自动匹配方式。                                                                                                                        | [使用镜像缓存CRD加速创建Pod](t1860150.html#topic-1860150)            |
| k8s.aliyun.com/eci-image-cache                 | "true"                         | 自动匹配镜像缓存。 **说明** 使用镜像缓存支持手动指定和自动匹配两种方式，建议使用自动匹配方式。                                                                                                                        | [使用镜像缓存CRD加速创建Pod](t1860150.html#topic-1860150)            |
| k8s.aliyun.com/acr-instance-id                 | cri-j36zhodptmyq\*\*\*\*       | ACR企业版实例ID。                                                                                                                                                                               | [配置ACR企业版免密](t1986148.html#topic-1986148)                  |
| k8s.aliyun.com/eci-eip-instanceid              | eip-bp1q5n8cq4p7f6dzu\*\*\*\*  | EIP实例ID。                                                                                                                                                                                  | [为ECI实例绑定EIP](t1860106.html#section-9su-qdv-b58)           |
| k8s.aliyun.com/eci-with-eip                    | "true"                         | 是否自动创建并绑定EIP。                                                                                                                                                                             | [为ECI实例绑定EIP](t1860106.html#section-9su-qdv-b58)           |
| k8s.aliyun.com/eip-bandwidth                   | 5                              | EIP带宽。                                                                                                                                                                                    | [为ECI实例绑定EIP](t1860106.html#section-9su-qdv-b58)           |
| k8s.aliyun.com/eip-common-bandwidth-package-id | cbwp-2zeukbj916scmj51m\*\*\*\* | 共享带宽包ID。                                                                                                                                                                                  | [为ECI实例绑定EIP](t1860106.html#section-9su-qdv-b58)           |
| k8s.aliyun.com/eip-isp                         | BGP                            | EIP线路类型，仅按量付费的EIP支持指定。取值范围： * BPG：BGP（多线）线路   * BGP_PRO：BGP（多线）精品线路                    | [为ECI实例绑定EIP](t1860106.html#section-9su-qdv-b58)           |
| k8s.aliyun.com/eip-internet-charge-type        | PayByBandwidth                 | EIP的计量方式。取值范围： * PayByBandwidth：按带宽计费   * PayByTraffic：按流量计费                           | [为ECI实例绑定EIP](t1860106.html#section-9su-qdv-b58)           |
| k8s.aliyun.com/eci-enable-ipv6                 | "true"                         | 是否分配IPv6。 **说明** 目前仅支持为Pod分配1个IPv6地址，k8s.aliyun.com/eci-enable-ipv6和k8s.aliyun.com/eci-ipv6-count的配置效果一致，您可以选择其一。                                                         | [配置IPv6地址](t1860116.html#topic-1860116)    |
| k8s.aliyun.com/eci-ipv6-count                  | 1                              | IPv6个数，目前仅支持配置1个。 **说明** 目前仅支持为Pod分配1个IPv6地址，k8s.aliyun.com/eci-enable-ipv6和k8s.aliyun.com/eci-ipv6-count的配置效果一致，您可以选择其一。                                                 | [配置IPv6地址](t1860116.html#topic-1860116)    |
| kubernetes.io/ingress-bandwidth                | 40M                            | 入方向带宽。                                                                                                                                                                                    | [ECI实例进行带宽限速](t1999563.html#topic-1999563)                 |
| kubernetes.io/egress-bandwidth                 | 20M                            | 出方向带宽。                                                                                                                                                                                    | [ECI实例进行带宽限速](t1999563.html#topic-1999563)                 |
| k8s.aliyun.com/eci-extra-ephemeral-storage     | 50Gi                           | 临时存储空间大小。                                                                                                                                                                                 | [自定义临时存储空间大小](t2043552.html#topic-2043552)                 |
| k8s.aliyun.com/eci-core-pattern                | /pod/data/dump/core            | Core dump文件保存目录。                                                                                                                                                                          | [查看Core dump文件](t1891689.html#topic-1891689)               |
| k8s.aliyun.com/eci-ntp-server                  | 100.100.\*.\*                  | NTP Server。                                                                                                                                                                               | [为Pod配置NTP服务](t1860142.html#topic-1860142) |



设置安全组 
--------------------------

Virtual Kubelet启动时，将通过环境变量设置默认的安全组。所有创建在虚拟节点上的Pod默认使用Virtual Kubelet配置的。如果您有特殊需求，可以通过添加Annotation的方式，为Pod设置特定的安全组。

配置示例如下：

```unknow
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo
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
            k8s.aliyun.com/eci-security-group: "sg-bp1dktddjsg5nktv****"      #设置安全组
        labels:
            app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
      nodeName: virtual-kubelet
```



多可用区创建Pod 
------------------------------

在创建Pod时，您可以指定多个交换机实现多可用区功能。系统将根据资源的库存情况，选择合适的可用区创建Pod。更多信息，请参见[多可用区创建实例](t1876030.html#topic-1876030)。

配置示例如下：

```yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    k8s.aliyun.com/eci-vswitch: "vsw-bp1xpiowfm5vo8o3c****,vsw-bp1rkyjgr1xwoho6k****"    #指定多个交换机ID
    k8s.aliyun.com/eci-schedule-strategy: "VSwitchOrdered"   #设置多可用区调度策略
  name: nginx-test
spec:
  containers:
  - name: nginx
    image: nginx:latest
```



设置RAM角色 
----------------------------

您可以通过添加Annotation的方式为Pod设置RAM角色，授予Pod访问阿里云产品的能力。
**注意**

请确保RAM角色的受信服务为云服务器。

配置示例如下：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: set-ram-role
  labels:
    app: vk
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
        annotations: 
            k8s.aliyun.com/eci-ram-role-name : "AliyunECIContainerGroupRole"   #设置RAM角色
        labels:
            app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
      nodeName: virtual-kubelet
```



指定实例规格创建Pod 
--------------------------------

您可以通过添加Annotation的方式为Pod指定允许使用的实例规格。在创建Pod时，如果遇到库存不足的情况，将按照顺序遍历指定的实例规格，保证创建成功率。更多信息，请参见[多规格创建实例](t1860149.html#topic-1860149)。
**说明**

实例规格支持直接指定vCPU和内存，或者指定具体的ECS规格。如果业务有特殊的规格需求，例如：GPU、高主频、本地盘等，请明确指定ECS规格。更多信息，请参见[指定ECS规格创建实例](t1860130.html#topic-1860130)。

配置示例如下：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vk-cos-use
  labels:
    app: cos
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cos
  template:
    metadata:
        annotations: 
            "k8s.aliyun.com/eci-use-specs": "2-4Gi,4-8Gi,ecs.c6.xlarge" #支持指定多个规格，规格可以是vCPU和内存，或者具体的ECS规格
        labels:
            app: cos
    spec:
      containers:
      - name: u1
        image: "registry-vpc.cn-beijing.aliyuncs.com/lxx/cos-4g"
      nodeName: virtual-kubelet
```



创建抢占式实例 
----------------------------

对于无状态应用、Job任务等，使用抢占式实例运行，可以有效地节约实例使用成本。您可以通过添加Annotation的方式，创建抢占式ECI示例。更多信息，请参见[创建抢占式实例](t1876161.html#topic-1876161)。

配置示例如下：

```yaml
apiVersion: apps/v1 # for versions before 1.8.0 use apps/v1beta1
kind: Deployment
metadata:
  name: nginx-deployment-basic
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      annotations:
        k8s.aliyun.com/eci-use-specs : "ecs.c5.large"  #根据需要替换ECS实例规格
        k8s.aliyun.com/eci-spot-strategy: "SpotWithPriceLimit"  #采用自定义设置价格上限的策略
        k8s.aliyun.com/eci-spot-price-limit: "0.250"   #设置每小时价格上限
    spec:
    #  nodeSelector:
    #    env: test-team
      containers:
      - name: nginx
        image: nginx:1.7.9 # replace it with your exactly <image_name:tags>
        ports:
        - containerPort: 80
```



自定义设置CPU选项 
-------------------------------

对于一台ECI实例，CPU选项由CPU物理核心数和每核线程数决定。根据您创建ECI实例的方式，部分ECI实例支持自定义CPU选项。更多信息，请参见[自定义CPU选项](t2020903.html#topic-2020903)。

配置示例如下：

```yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    k8s.aliyun.com/eci-use-specs : "ecs.c6.2xlarge"    #指定支持自定义CPU选项的ECS规格
    k8s.aliyun.com/eci-cpu-option-core: 2                 #自定义设置CPU物理核心数为2
    k8s.aliyun.com/eci-cpu-option-ht: 1                    #自定义设置每核线程数为1，即关闭超线程
  name: nginx-test
spec:
  containers:
  - name: nginx
    image: nginx:latest
  restartpolicy: Always
```



设置ECI重调度 
-----------------------------

调度Pod到虚拟节点时，可能会碰到调度失败的情况，您可以通过添加Annotation的方式为Pod开启重调度，即使异步调度失败了，仍会一直保持调度，不返回失败。

配置示例如下：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: set-eci
  labels:
    app: vk
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
        annotations: 
            k8s.aliyun.com/eci-reschedule-enable: "true"    #开启ECI重调度
        labels:
            app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
      nodeName: virtual-kubelet
```



Pod创建失败置为Failed 
------------------------------------

默认情况下，每个Pod在创建时，如果遇到错误，会重试一定次数，如果还是Failed的话，则Pod会处于pending状态。对于一些Job类型的任务，您可能希望直接体现Failed状态。此时，您可以通过添加Annotation的方式，设置Pod遇到创建失败时，体现Failed状态。

配置示例如下：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: set-pod-fail-on-create-err
  labels:
    app: vk
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
        annotations: 
            k8s.aliyun.com/pod-fail-on-create-err: "true"  #设置Pod创建失败时，状态为Failed
        labels:
            app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
      nodeName: virtual-kubelet
```



使用镜像缓存CRD加速创建Pod 
-------------------------------------

使用镜像缓存技术可以加速创建Pod，您可以通过添加Annotation的方式，为Pod指定使用的镜像缓存，或者开启自动匹配镜像缓存。更多信息，请参见[使用镜像缓存CRD加速创建Pod](t1860150.html#topic-1860150)。

配置示例如下：

* 指定镜像缓存

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    annotations:
      k8s.aliyun.com/eci-image-snapshot-id: imc-2ze5tm5gehgtiiga****   #指定使用的镜像缓存
    name: nginx-imagecache-id
  spec:
    containers:
    - image: nginx:1.7.9
      imagePullPolicy: IfNotPresent
      name: nginx
      resources:
        limits:
          cpu: 300m
          memory: 200Mi
        requests:
          cpu: 200m
          memory: 100Mi
    nodeName: virtual-kubelet
  ```

  

* 自动匹配

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    annotations:
      k8s.aliyun.com/eci-image-cache: "true"    #开启自动匹配镜像缓存
    name: nginx-auto-match
  spec:
    containers:
    - image: nginx:1.7.9
      imagePullPolicy: IfNotPresent
      name: nginx
      resources:
        limits:
          cpu: 300m
          memory: 200Mi
        requests:
          cpu: 200m
          memory: 100Mi
    nodeName: virtual-kubelet
  ```

  




指定ACR企业版实例 
-------------------------------

阿里云容器服务ACR支持免密拉取，您可以通过添加Annotation的方式指定ACR企业版实例，从对应的镜像仓库中拉取镜像。更多信息，请参见[配置ACR企业版免密](t1986148.html#topic-1986148)。

配置示例如下：

```yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    k8s.aliyun.com/acr-instance-id: cri-j36zhodptmyq****      #指定ACR企业版实例
  name: cri-test
spec:
  containers:
  - image: test****-registry.cn-beijing.cr.aliyuncs.com/eci_test/nginx:1.0   #使用公网拉取镜像
    imagePullPolicy: Always
    name: nginx
  restartPolicy: Never
```



绑定弹性公网IP 
-----------------------------

如果Pod有公网通信的需求，您可以为其绑定EIP。更多信息，请参见[如何给Pod挂载弹性公网IP](t220325.html#task-1846800)。

配置示例如下：

* 自动创建

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: nginx
    annotations:
      k8s.aliyun.com/eci-with-eip: "true"    #开启自动创建EIP
      k8s.aliyun.com/eip-bandwidth: "10"     #设置带宽，默认为5，单位为Mbps
  spec:
    containers:
    - image: registry-vpc.cn-hangzhou.aliyuncs.com/jovi/nginx:alpine
      imagePullPolicy: Always
      name: nginx
      ports:
      - containerPort: 80
        name: http
        protocol: TCP
    restartPolicy: OnFailure
  ```

  

* 指定EIP

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: nginx
    annotations:
      k8s.aliyun.com/eci-eip-instanceid: "eip-bp1q5n8cq4p7f6dzu****"   #指定EIP
  spec:
    containers:
    - image: registry-vpc.cn-hangzhou.aliyuncs.com/jovi/nginx:alpine
      imagePullPolicy: Always
      name: nginx
      ports:
      - containerPort: 80
        name: http
        protocol: TCP
    restartPolicy: OnFailure
  ```

  




配置IPv6 
---------------------------

相比IPv4，IPv6不仅可以解决网络地址资源有限的问题，还可以解决多种接入设备连入互联网障碍的问题。您可以通过添加Annotation的方式为Pod分配IPv6地址。更多信息，请参见[配置IPv6地址](t1860116.html#topic-1860116)。

配置示例如下：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  annotations:
    k8s.aliyun.com/eci-enable-ipv6: "true"    #开启自动分配IPv6
spec:
  containers:
  - name: nginx
    image: nginx
  nodeName: virtual-kubelet
```



设置出入方向带宽 
-----------------------------

CI支持配置流入和流出的网络带宽值。您可以通过添加Annotation的方式，为Pod指定出方向和入方向的带宽值进行限速。更多信息，请参见[ECI实例进行带宽限速](t1999563.html#topic-1999563)。

配置示例如下：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: eci-qos
  annotations:
    kubernetes.io/ingress-bandwidth: 40M    #设置入方向带宽
    kubernetes.io/egress-bandwidth: 10M    #设置出方向带宽
spec:
  containers:
  - name: nginx
    image: nginx:latest
    command: ["bash","-c","sleep 100000"]
```



设置临时存储空间大小 
-------------------------------

ECI实例默认提供20 GiB的免费存储空间，如果该存储空间大小无法满足您的需求，您可以通过添加Annotation的方式，自定义增加临时存储空间大小。更多信息，请参见[自定义临时存储空间大小](t2043552.html#topic-2043552)。

配置示例如下：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test
  annotations:
    k8s.aliyun.com/eci-extra-ephemeral-storage: "50Gi"  #自定义设置临时存储空间大小
spec:
  containers:
  - name: nginx
    image: nginx:latest
    imagePullPolicy: IfNotPresent
  restartPolicy: Always
  nodeName: virtual-kubelet
```



设置Core dump文件的保存目录 
---------------------------------------

容器进程异常退出会产生Core dump文件，默认情况下，Core dump文件的名称为core.pid，保存在当前目录下。您可以通过添加Annotation的方式，自定义设置Core dump文件保存目录。更多信息，请参见[查看Core dump文件](t1891689.html#topic-1891689)。

配置示例如下：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test
  annotations:
    k8s.aliyun.com/eci-core-pattern: "pod/data/dump/core"  # 设置Core dump文件保存目录
spec:
  containers:
  - image: nginx:latest
    name: test-container
    volumeMounts:
    - mountPath: /pod/data/dump/
      name: default-volume
  volumes:
  - name: nfs
    nfs:
      server: 143b24****-gfn3.cn-beijing.nas.aliyuncs.com
      path: /dump/
      readOnly: false
```



为Pod设置NTP服务 
--------------------------------

您可以为Pod添加k8s.aliyun.com/eci-ntp-server的Annotation来配置NTP服务。更多信息，请参见[为Pod配置NTP服务](t1860142.html#topic-1860142)。

配置示例如下：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: set-ngnix-ntp
  labels:
    app: vk
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
        annotations: 
            k8s.aliyun.com/eci-ntp-server: 100.100.5.*,100.100.5.*  #设置NTP服务器地址
        labels:
            app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
      nodeName: virtual-kubelet
```


