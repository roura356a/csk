使用镜像缓存CRD加速创建Pod 
=====================================

阿里云以CRD的方式将ECI的镜像缓存功能提供给Kubernetes用户，以便Kubernetes用户也可以使用该功能来加速创建Pod。本文介绍如何使用镜像缓存CRD加速创建Pod。

背景信息 
-------------------------

ECI实例在创建时，大部分时间消耗在镜像下载阶段。为加速实例创建速度，ECI提供镜像缓存功能。您可以预先将需要使用的镜像制作成云盘快照缓存，然后基于该快照来创建实例，避免或者减少镜像层的下载，从而提升实例创建速度。

经实测，以Docker Hub的flink镜像（约386.26 MB）创建Pod为例，正常创建ECI实例过程中，镜像准备阶段需要耗时50s，使用镜像缓存后，镜像准备阶段可以缩短至5s，可以极大节约实例创建耗时。
**说明**

具体提升速度由Pod中使用的镜像个数、镜像大小和镜像仓库网络因素等决定。

为方便Kubernetes用户也可以使用到ECI的镜像缓存功能，阿里云以CRD的方式将镜像缓存功能提供给Kubernetes用户，对应的CRD为ImageCache。
**注意**

ImageCache CRD在kubernetes集群中为Cluster级别资源，被集群内所有Namespace共享。

准备工作 
-------------------------

登录kubernetes集群，执行以下命令，验证您的kubernetes集群是否已经支持ImageCache。

```unknow
kubectl get crd/imagecaches.eci.alibabacloud.com
```



* 如果返回Error信息，则表示您的kubernetes集群不支持ImageCache，返回示例如下：

  ```unknow
  Error from server (NotFound): customresourcedefinitions.apiextensions.k8s.io "imagecaches.eci.alibabacloud.com" not found
  ```

  

  如果您的kubernetes集群不支持ImageCache，您需要升级Virtual Kubelet（VK）到相应支持ImageCache的版本。
  **说明**

  建议您升级VK到最新版本，以便更好地使用新功能。关于VK的版本信息，请参见[Virtual Kubelet版本记录](/cn.zh-CN/产品发布记录/组件介绍与变更记录/其他/ack-virtual-node.md)。
  

* 如果返回imagecaches.eci.alibabacloud.com的信息，则表示您的kubernetes集群已经支持ImageCache，返回示例如下：

  ```unknow
  NAME                                                CREATED AT
  imagecaches.eci.alibabacloud.com                    2019-09-27T01:15:07Z
  ```

  

  如果您的kubernetes集群的ImageCache CRD不是最新的，建议您执行kubectl apply命令重新部署。

  ```shell
  kubectl apply -f imagecache-crd-sample.yaml
  ```

  

  imagecache-crd-sample.yaml的内容示例如下：

  ```yaml
  apiVersion: apiextensions.k8s.io/v1beta1
  kind: CustomResourceDefinition
  metadata:
    name: imagecaches.eci.alibabacloud.com
  spec:
    group: eci.alibabacloud.com
    version: v1
    names:
      kind: ImageCache
      plural: imagecaches
      shortNames:
      - ic
      categories:
      - all
    scope: Cluster
    subresources:
      status: {}
    validation:
      openAPIV3Schema:
        required:
        - spec
        properties:
          spec:
            type: object
            required:
            - images
            properties:
              imagePullSecrets:
                type: array
                items:
                  type: string
              images:
                minItems: 1
                type: array
                items:
                  type: string
              imageCacheSize:
                type: integer
              retentionDays:
                type: integer
    additionalPrinterColumns:
    - name: Age
      type: date
      JSONPath: .metadata.creationTimestamp
    - name: CacheId
      type: string
      JSONPath: .status.imageCacheId
    - name: Phase
      type: string
      JSONPath: .status.phase
    - name: Progress
      type: string
      JSONPath: .status.progress
  ```

  




管理ImageCache 
---------------------------------

创建CustomResourceDefinition对象后，您可以操作ImageCache资源。更多信息，请参见[kubernetes ImageCache API](t2059054.html#topic-2059054)。

ImageCache的YAML配置文件如下：

```yaml
apiVersion: eci.alibabacloud.com/v1
kind: ImageCache
metadata:
  name: imagecache-sample
  annotations:
    k8s.aliyun.com/imc-enable-reuse: "true" #开启镜像缓存复用
spec:
  images:
  - centos:latest
  - busybox:latest
  imagePullSecrets:
  - default:secret1
  - default:secret2
  - kube-system:secret3
  imageCacheSize:
   25
  retentionDays:
   7
```


**说明**

开启镜像缓存复用后，在创建镜像缓存的过程中，系统将自动匹配已有镜像缓存，如果新的镜像缓存与已有镜像缓存存在重复的镜像层，新的镜像缓存将复用已有镜像缓存的镜像层，从而加速镜像缓存的创建。

相关参数说明如下：


|          名称           |     类型     | 是否必选 |                                                                                                               描述                                                                                                                |
|-----------------------|------------|------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| spec.images           | String\[\] | 是    | 用于创建镜像缓存的容器镜像列表。                                                                                                                                                                                                                |
| spec.imagePullSecrets | String\[\] | 否    | 镜像仓库对应的Secret列表。 * 如果镜像列表中包含私有仓库的镜像，则需要为私有镜像仓库创建Secret，然后按照`namespace:secretName`的格式设置该参数。   * 如果镜像列表中的镜像均为公有镜像，则无需设置该参数。    |
| spec.imageCacheSize   | int        | 否    | 镜像缓存的大小，即用于创建镜像缓存快照的云盘大小。默认为20 GB。取值范围为20\~32768 GB。                                                                                                                                                                            |
| spec.retentionDays    | int        | 否    | 镜像缓存保留时间，过期将会被清理。单位为天。默认永不过期。                                                                                                                                                                                                   |



### 创建ImageCache 

1. 创建ImageCache。

   ```shell
   kubectl create -f imagecache-secrets-test.yaml
   ```

   

   imagecache-secrets-test.yaml的内容示例如下：

   ```yaml
   apiVersion: eci.alibabacloud.com/v1
   kind: ImageCache
   metadata:
     name: imagecache-sample
     annotations:
       k8s.aliyun.com/imc-enable-reuse: "true" #开启镜像缓存复用
   spec:
     images:
     - centos:latest
     - busybox:latest
     imagePullSecrets:
     - default:secret1
     - default:secret2
     - kube-system:secret3
     imageCacheSize:
      25
     retentionDays:
      7
   ```

   

2. 查看ImageCache状态。

   ```shell
   kubectl get imagecache imagecache-sample-test
   ```

   

   返回结果示例如下：

   ```shell
   NAME                              AGE   CACHEID                               PHASE   PROGRESS
   imagecache-sample-test            20h   imc-2zeditzeoemfhqor****              Ready    100%
   ```

   




### 查询ImageCache 

您可以根据需要查询集群下所有ImageCache列表或者查看某个ImageCache的详细信息：

* 查询集群下所有ImageCache列表

  ```shell
  kubectl get imagecache
  ```

  

* 查看某个ImageCache的详细信息

  ```shell
  kubectl get imagecache/imagecache-sample-test -o yaml
  ```

  




### 删除ImageCache 

如果想要删除某个ImageCache，可执行如下命令：

```shell
kubectl delete imagecache/imagecache-sample-test
```



使用ImageCache加速创建Pod 
----------------------------------------

ImageCache资源是Cluster级别，因此在不同的namespace下创建Pod时均可以使用ImageCache来实现加速创建Pod。

使用ImageCache创建Pod包括明确指定和自动匹配两种方式，您可以在Pod级别的metadata中添加Annotation来配置，相关配置项如下：

* k8s.aliyun.com/eci-image-snapshot-id：明确指定使用哪个镜像缓存创建Pod。

  

* k8s.aliyun.com/eci-image-cache：根据匹配策略自动匹配最优的ImageCache创建Pod。

  



**说明**

如果同时设置`k8s.aliyun.com/eci-image-snapshot-id`和`k8s.aliyun.com/eci-image-cache`，则明确指定方式的优先级高于自动匹配方式。

使用ImageCache创建Pod时，请注意以下事项：

* Pod中容器的镜像建议采用ImageCache中的镜像，以提高匹配度。

  

* Pod中容器的镜像拉取策略（ImagePullPolicy）建议设置为按需拉取（IfNotPresent），以避免镜像层重复下载。

  




### 明确指定 

创建Pod时，您可以通过添加Annotation的方式，声明使用指定的ImageCache来加速创建Pod。
**注意**

请确保指定的ImageCache的状态为Ready，其它状态的ImageCache会导致Pod创建失败。

* Deployment示例

  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: nginx-deployment
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
          k8s.aliyun.com/eci-image-snapshot-id: imc-2ze5tm5gehgtiiga****  #明确指定ImageCache
      spec:
        nodeName: virtual-kubelet
        containers:
        - name: nginx
          image: nginx:1.7.9
          imagePullPolicy: IfNotPresent
  ```

  

* Pod示例

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    annotations:
      k8s.aliyun.com/eci-image-snapshot-id: imc-2ze5tm5gehgtiiga****  #明确指定ImageCache
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

  




### 自动匹配 

创建Pod时，您可以通过添加Annotation的方式，声明使用自动匹配的ImageCache来加速创建Pod。ECI将基于您已有的ImageCache列表，根据匹配策略进行匹配，选择最优的ImageCache来创建Pod。匹配策略的优先级从高到低依次为：镜像匹配度、匹配的镜像大小、创建时间。
**说明**

如果没有匹配到合适的ImageCache，则将执行正常下载镜像的流程来创建Pod。

* Deployment示例

  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: nginx-deployment
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
          k8s.aliyun.com/eci-image-cache: "true"   #开启自动匹配ImageCache
      spec:
        nodeName: virtual-kubelet
        containers:
        - name: nginx
          image: nginx:1.7.9
          imagePullPolicy: IfNotPresent
  ```

  

* Pod示例

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    annotations:
      k8s.aliyun.com/eci-image-cache: "true"  #开启自动匹配ImageCache
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

  



