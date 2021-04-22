ECI Pod Annotation 
=======================================

Kubernetes集群通过虚拟节点创建Pod到ECI时，为充分利用ECI系统提供的功能，在不改变Kubernetes语义前提下，ECI根据需求增加Annotation。 本文主要介绍ECI目前支持的`Annotation列表`。


**说明**

本文介绍的注解，仅适用于创建到

**虚拟节点** 

上的Pod，调度到ECS上的Pod不受这些annotation影响。

指定ECI规格 
----------------------------

增加`k8s.aliyun.com/eci-use-specs`注解， 创建Pod时允许使用的实例规格列表，遇到没库存时，按照顺序遍历规格列表购买ECI实例，应对库存问题。

k8s.aliyun.com/eci-use-specs的值，支持列表，使用逗号分隔，列表中元素的格式：

1.模糊匹配

* `${cpu}-${mem}Gi`

  ：例如: 2-4Gi；表示创建一个

  `2核4G`

  的ECI实例。模糊匹配仅支持普通规格，对于本地SSD、GPU等实例需设置明确的实例规格。
  




模糊配置方式：




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
                "k8s.aliyun.com/eci-use-specs": "2-4Gi,4-8Gi,ecs.c6.xlarge"
            labels:
                app: cos
        spec:
          containers:
          - name: u1
            image: "registry-vpc.cn-beijing.aliyuncs.com/lxx/cos-4g"
          nodeName: virtual-node-eci-0





2.精确设置：明确指定创建ECI使用的实例规格，例如：ecs.c6.xlarge，ECI支持的实例规格，请参见[实例规格族](/intl.zh-CN/实例/实例规格族.md)。

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
                "k8s.aliyun.com/eci-use-specs": "ecs.c5.xlarge,ecs.c6.xlarge,ecs.c6.4xlarge"
            labels:
                app: cos
        spec:
          containers:
          - name: u1
            image: "registry-vpc.cn-beijing.aliyuncs.com/lxx/cos-4g"
          nodeName: virtual-node-eci-0



ImageCache 
-------------------------------

创建Pod时，利用镜像缓存技术，加速创建速度。请参见[使用镜像缓存 CRD 加速创建 Pod]()。

手工指定 
-------------------------

创建Deployment时，可以指定已有ImageCacheID，加速Deployment创建。

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-with-imagecache
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          annotations: 
              "k8s.aliyun.com/eci-image-snapshot-id": "${your_image_cache_id}"
          labels:
              app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9
            imagePullPolicy: IfNotPresent
          nodeName: virtual-node-eci-0



自动匹配 
-------------------------

创建Deployment时，开启自动匹配镜像缓存，在创建过程中，会根据用户已有的镜像缓存，选择最优的镜像缓存，加速Deployment创建。

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-dynamic-image-cache
      labels:
        app: nginx-dynamic-image-cache
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
            annotations: 
                "k8s.aliyun.com/eci-image-cache": "true"
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
            imagePullPolicy: IfNotPresent
          nodeName: virtual-node-eci-0



弹性公网 IP 
----------------------------

通过注解为ECI实例绑定弹性公网IP，赋予ECI实例公网访问能力，弹性公网IP详细信息，请参见[什么是弹性公网IP](/intl.zh-CN/.md)。

自动创建 
-------------------------

创建Deployment时，同时创建弹性公网IP，绑定到ECI实例。

自动创建的公网IP信息：

* 带宽默认为 **5M** ，可以通过注解调整。

  




<!-- -->

* 计费方式为 **按量计费。**

  



**说明**

* 您可以通过Annotation k8s.aliyun.com/eip-bandwidth指定EIP的带宽，默认值为5，单位为M。

  

* 您也可以通过Annotation k8s.aliyun.com/eip-common-bandwith-package-id让EIP绑定共享带宽。

  




    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: cos-vk-resource-group-id
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
                "k8s.aliyun.com/eci-with-eip": "true"
                "k8s.aliyun.com/eip-bandwidth": "10"
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-node-eci-0



手工指定 
-------------------------

创建Deployment时，选择已有弹性公网IP，绑定到ECI实例。

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: cos-vk-resource-group-id
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
                "k8s.aliyun.com/eci-eip-instanceid": "${your_eip_Instance_Id}"
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-node-eci-0



Pod创建失败置为Failed 
------------------------------------

默认情况下, 每个Pod在创建的时候, 如果遇到错误, 会重试一定次数。如果依然还是失败的话, 则会处于pending状态。

对于一些job类型的任务希望直接体现失败状态，可以在pod的annotation中设置 k8s.aliyun.com/pod-fail-on-create-err 为true, 遇到创建失败时, Pod会处于Failed状态。

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
                "k8s.aliyun.com/pod-fail-on-create-err": "true"
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-node-eci-0



设置NTP服务 
----------------------------

通过注解为ECI实例设置ntp server，支持设置多个。请参见[为 Pod 配置 NTP 服务]()。

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
                "k8s.aliyun.com/eci-ntp-server": 100.100.5.1,100.100.5.2  # 设置您的NTP服务器地址
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-node-eci-0



设置资源组 
--------------------------

通过注解为ECI实例设定资源组。

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: cos-vk-resource-group-id
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
                "k8s.aliyun.com/eci-resource-group-id" : "${your_resource_group_id}"
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-node-eci-0



设置RamRole 
------------------------------

通过注解为ECI实例设置已有RamRole，赋予在ECI实例内部可以访问阿里云产品能力。

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
                "k8s.aliyun.com/eci-ram-role-name" : "${your_ram_role_name}"
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-node-eci-0



设置虚拟交换机 
----------------------------

virtual-kubelet启动时，通过环境变量`ECI_VSWITCH`设置默认虚机交换机，所有创建在虚拟机节点上的Deployment默认使用virtual-kubelet配置的虚拟交换机创建ECI实例，如果用户希望使用同VPC下其他虚拟交换机创建ECI实例，可以通过注解方式显示指定虚拟交换机。

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: set-vswitch
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
                "k8s.aliyun.com/eci-vswitch" : "${your_vsw_id}"
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-node-eci-0



设置安全组 
--------------------------

virtual-kubelet启动时，通过环境变量`ECI_SECURITY_GROUP`设置默认安全组，所有创建在虚拟机节点上的Deployment默认使用virtual-kubelet配置的安全组创建ECI实例，如果用户希望使用同VPC下其他安全组创建ECI实例，可以通过注解方式显示指定安全组。

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: set-security-group
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
                "k8s.aliyun.com/eci-security-group" : "${your_security_group_id}"
            labels:
                app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
          nodeName: virtual-node-eci-0


