使用镜像缓存CRD加速创建Pod 
=====================================

ECI容器组实例创建过程中，大部分时间消耗在镜像下载阶段，为加速ECI容器组创建速度，ECI提供镜像缓存功能，用户事先将需要用到的镜像作为云盘快照缓存，在创建ECI容器组实例时基于快照创建，避免或减少镜像层下载，从而提升ECI容器组实例创建速度。经实测，基于dockerhub的flink镜像（386.26 MB）创建Pod，普通创建eci过程中，镜像准备阶段需要耗费`50s`，使用镜像快照后镜像准备阶段仅需`5s`。因此在创建eci过程中，镜像准备阶段节省时间`45s`。具体的提升速度跟Pod使用的镜像个数，镜像大小和镜像仓库网络因素决定。

为方便kubernetes用户也可以使用到ECI的镜像缓存功能，因此以CRD的方式将ECI镜像缓存功能暴露给kubernetes用户。重点强调：ImageCache CRD在kubernetes集群中为 **Cluster级别的资源** ，类似于PV，被集群内所有namespace共享。

准备工作 
-------------------------

验证您的kubernetes集群是否支持ImageCache。输入以下命令：

    kubectl get crd/imagecaches.eci.alibabacloud.com



* 如果您的kubernetes集群已经支持ImageCache，系统输出如下结果：

  






    NAME                                                CREATED AT
    imagecaches.eci.alibabacloud.com   2019-09-27T01:15:07Z



* 如果您的kubernetes集群不支持ImageCache，需要升级`virtual-kubelet`实现支持ImageCache，系统输出如下结果：

  






    Error from server (NotFound): customresourcedefinitions.apiextensions.k8s.io "imagecaches.eci.alibabacloud.com" not found



virtual-kubelet升级方式：

* 阿里云serverless kubernetes集群：由管理员统一负责升级

  

* 阿里云托管kubernetes集群：用户更新

  

* 阿里云专有kubernetes集群：用户更新

  

* 阿里云自建kubernetes集群：用户更新

  




准备工作完成后，您可以在您的kubernetes集群中，创建ImageCache资源，并且可以使用ImageCache创建Pod。

如果您的集群的ImageCache CRD不是最新的，不支持ImageCache CRD新增的参数，建议升级完virtual-kubelet后，输入以下命令：

    kubectl apply -f imagecache-crd-sample.yaml



imagecache-crd-sample.yaml：

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



ImageCache基本操作 
-----------------------------------

创建CustomResourceDefinition对象后，您可以操作ImageCache资源。

**ImageCache全参数YAML文件：** 

    apiVersion: eci.alibabacloud.com/v1
    kind: ImageCache
    metadata:
      name: imagecache-sample
      annotations:
        k8s.aliyun.com/auto-match-imagecache: "true" #开启镜像缓存复用
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
                            



**参数说明：** 


|          名称           |     类型     | 必选 |                                                              描述                                                               |
|-----------------------|------------|----|-------------------------------------------------------------------------------------------------------------------------------|
| spec.images           | \[\]string | Y  | 镜像列表。                                                                                                                         |
| spec.imagePullSecrets | \[\]string | N  | 镜像仓库对应的Secret列表，格式：。如果images列表中有用户私有仓库镜像，需要用户为私有镜像仓库创建secret，然后根据 **namespace:secretName** 格式，设置此参数。如若images中均是共有镜像，不需要设置此参数。 |
| spec.imageCacheSize   | int        | N  | 镜像缓存使用的快照盘大小，默认为20 GB，取值范围为:\[20, 32768\]，单位为GB。                                                                              |
| spec.retentionDays    | int        | N  | 镜像缓存保留的时间，取值范围\[1,65536\]，默认永不过期。                                                                                             |



创建ImageCache 
---------------------------------


**说明**

如果您已经创建过镜像缓存，且新创建的镜像缓存与已有镜像缓存存在重复的镜像层，可通过开启自动匹配加速镜像拉取的时间从而加速镜像缓存的创建。

输入以下命令创建ImageCahce：

    # kubectl create -f  imagecache-secrets-test.yaml



创建ImageCahce完成后，系统输出类似如下结果：



    apiVersion: eci.alibabacloud.com/v1
    kind: ImageCache
    metadata:
      name: imagecache-sample-liu
      annotations:
        k8s.aliyun.com/auto-match-imagecache: "true" #开启镜像缓存复用
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



如果查看ImageCache状态，输入以下命令： 



    liumihustdeMacBook-Pro:vk-debug liumihust$ kubectl get imagecache imagecache-sample-liu



系统输出类似如下结果：

    NAME                              AGE   CACHEID                               PHASE   PROGRESS
    imagecache-sample-liu   20h   imc-2zeditzeoemfhqor4rf8   Ready    100%



查询ImageCache 
---------------------------------

如果获取Cluster下全部ImageCache列表，输入以下命令：

    kubectl get imagecache imagecache-sample-liu -o yaml



如果查看ImageCache详情（包括属性、状态以及事件），输入以下命令：



    kubectl get imagecache/imagecache-secrets-test -o yaml



系统输出类似如下结果：

    apiVersion: eci.alibabacloud.com/v1
    kind: ImageCache
    metadata:
      creationTimestamp: "2019-10-24T13:40:56Z"
      generation: 1
      name: imagecache-sample-liu
      resourceVersion: "212484735"
      selfLink: /apis/eci.alibabacloud.com/v1/imagecaches/imagecache-sample-liu
      uid: e83a1c53-f663-11e9-a0d4-fa13fc175b32
    spec:
      imageCacheSize: 25
      images:
      - busybox:latest
      - nginx
    status:
      events:
      - count: 1
        eventTime: null
        firstTimestamp: "2019-10-24T13:41:40Z"
        involvedObject: {}
        lastTimestamp: "2019-10-24T13:41:40Z"
        message: Success pull image busybox:latest
        metadata:
          creationTimestamp: null
          name: busybox:latest
        reportingComponent: ""
        reportingInstance: ""
        source:
          component: kubelet
          host: eci
        type: Normal
      - count: 1
        eventTime: null
        firstTimestamp: "2019-10-24T13:42:04Z"
        involvedObject: {}
        lastTimestamp: "2019-10-24T13:42:04Z"
        message: Success pull image nginx
        metadata:
          creationTimestamp: null
          name: nginx
        reportingComponent: ""
        reportingInstance: ""
        source:
          component: kubelet
          host: eci
        type: Normal
      - count: 1
        eventTime: null
        firstTimestamp: "2019-10-24T13:42:06Z"
        involvedObject: {}
        lastTimestamp: "2019-10-24T13:42:06Z"
        message: Success pull image registry-vpc.cn-beijing.aliyuncs.com/acs/pause-amd64:3.0
        metadata:
          creationTimestamp: null
          name: registry-vpc.cn-beijing.aliyuncs.com/acs/pause-amd64:3.0
        reportingComponent: ""
        reportingInstance: ""
        source:
          component: kubelet
          host: eci
        type: Normal
      imageCacheId: imc-2zeditzeoemfhqor4rf8
      expireDateTime: "2019-10-25T13:40:54Z"
      lastUpdatedTime: "2019-10-24T13:44:17Z"
      phase: Ready
      progress: 100%
      startTime: "2019-10-24T13:40:58Z"



ImageCache快速创建Pod 
--------------------------------------

ImageCache资源是Cluster级别的，所以在不同的namespace下创建Pod都可以使用ImageCache实现创建Pod。

使用ImageCache快速创建Pod有两种使用方式：

* 明确指定方式：在创建pod时，明确的指定使用哪个ImageCacheId创建Pod。

  

* 自动匹配方式：在创建Pod时，根据匹配策略自动选择最优的ImageCache创建Pod。

  




**明确指定方式** 

这种使用方式，有一个前提条件就是ImageCache状态必须为`Ready`状态，其他状态的ImageCache会导致Pod创建失败。

**Deployment使用方式** 

在PodTemplate增加Annotation关联ImageCache：

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
            k8s.aliyun.com/eci-image-snapshot-id: imc-2ze5tm5gehgtiigaz5jh
        spec:
          nodeName: virtual-kubelet
          containers:
          - name: nginx
            image: nginx:1.7.9
            imagePullPolicy: IfNotPresent



**Pod使用方式** 

在Pod的Yaml文件中增加Annotation关联ImageCache：

    apiVersion: v1
    kind: Pod
    metadata:
      annotations:
        k8s.aliyun.com/eci-image-snapshot-id: imc-2ze5tm5gehgtiigaz5jh
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



**自动匹配方式** 

在创建Pod时，用户通过Annotation方式，声明使用自动匹配ImageCache方式快速创建Pod，ECI管控基于用户已有的ImageCache列表依据策略进行匹配，匹配出最优的ImageCache创建Pod。如果没有匹配到合适的ImageCache，执行正常下载镜像创建Pod流程。

目前匹配策略的优先级从高到底：

* 镜像匹配度

  

* 匹配的镜像大小

  

* 创建时间

  




**Deployment使用方式** 

在PodTemplate增加Annotation关联ImageCache，告知ECI管控在创建Pod时是否启用自动匹配ImageCache：

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
            k8s.aliyun.com/eci-image-cache: "true"
        spec:
          nodeName: virtual-kubelet
          containers:
          - name: nginx
            image: nginx:1.7.9
            imagePullPolicy: IfNotPresent



**Pod使用方式** 

基于Annotation方式，在Pod的Yaml文件中增加Annotation：

    apiVersion: v1
    kind: Pod
    metadata:
      annotations:
        k8s.aliyun.com/eci-image-cache: "true"
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



使用建议 
-------------------------

* Pod中容器的`image`建议采用ImageCache中镜像，提高匹配度。

  

* Pod中容器的ImagePullPolicy建议设置为IfNotPresent，避免镜像层重复下载。

  





**说明**

* 在创建Pod时，`k8s.aliyun.com/eci-image-snapshot-id`和`k8s.aliyun.com/eci-image-cache`同时设置时，明确指定方式优先级高于自动匹配方式。

  

* 如果是deployment，使用cache的Annotation应该放在pod级别，而不是放在deployment级别，否则会设置失效。

  




删除ImageCache 
---------------------------------

删除ImageCache时，不影响基于ImageCache创建成功的Pod实例。

1. 查看ImageCache，执行如下命令：

       kubectl get imagecache/imagecache-secrets-test

   

   系统输出类似如下结果： 

       NAME                                AGE   CACHEID                                 PHASE   PROGRESS
       imagecache-secrets-test   14h   imc-2ze5tm5gehgtjru0kfm8   Ready    100%

   

2. 删除ImageCache，执行如下命令：

       kubectl delete imagecache/imagecache-secrets-test

   

   系统输出类似如下结果：

       imagecache.eci.alibabacloud.com " imagecache-secrets-test" deleted
       kubectl get imagecache/ imagecache-secrets-test
       Error from server (NotFound): imagecaches.eci.alibabacloud.com " imagecache-secrets-test" not found        

   

3. 确认ImageCache是否删除。执行如下命令：

       kubectl get imagecache/ imagecache-secrets-test

   

   系统输出类似如下结果表明ImageCache已删除。

       Error from server (NotFound): imagecaches.eci.alibabacloud.com " imagecache-secrets-test" not found

   




kubernetes ImageCache API文档 
------------------------------------------------



|        Group         | Version |    Kind     |
|----------------------|---------|-------------|
| eci.alibabacloud.com | v1      | ImageCache。 |



ImageCache v1 eci.alibabacloud.com 
-------------------------------------------------------

**ImageCacheList:** 


|   Field    |      Type      |          Description           |
|------------|----------------|--------------------------------|
| apiVersion | string         | api版本： eci.alibabacloud.com/v1 |
| kind       | string         | 资源类别：ImageCacheList            |
| metadata   | ListMeta       | Standard list metadata。        |
| Items      | \[\]ImageCache | 镜像缓存列表。                        |



**ImageCache:** 


|   Field    |       Type       |          Description          |
|------------|------------------|-------------------------------|
| apiVersion | string           | api版本：eci.alibabacloud.com/v1 |
| kind       | string           | 资源类别：ImageCache               |
| metadata   | ObjectMeta       | 对象metadata。                   |
| spec       | ImageCacheSpec   | 镜像缓存详细信息。                     |
| status     | ImageCacheStatus | 镜像缓存状态信息。                     |



**ImageCacheSpec:** 


|      Field       |    Type    | Request |                  Description                   |
|------------------|------------|---------|------------------------------------------------|
| images           | \[\]string | Y       | 镜像缓存的镜像列表。                                     |
| imagePullSecrets | \[\]string | N       | 私有镜像仓库对应的Secret列表，格式： **namespace:secretName** |
| imageCacheSize   | int        | N       | 镜像缓存的大小，默认为20 GB，取值范围为：\[20, 32768\]，单位为GB。    |
| retentionDays    | int        | N       | 镜像缓存保留的时间，取值范围\[1,65536\]，默认永不过期。              |



**ImageCacheStatus:** 


|      Field      |   Type    |                         Description                         |
|-----------------|-----------|-------------------------------------------------------------|
| phase           | string    | 镜像缓存状态。                                                     |
| progress        | string    | 标示制作过程中进度。                                                  |
| imageCacheId    | string    | 镜像缓存ID，调用Provdier的Create操作返回，然后更新到ImageCache对象。             |
| startTime       | Time      | 镜像缓存对象创建时间。                                                 |
| lastUpdatedTime | Time      | 镜像缓存最后更新时间。                                                 |
| events          | \[\]Event | 制作镜像cache过程中的事件信息。                                          |
| expireDateTime  | Time      | 镜像缓存的过期时间，有两种来源：1、用户设置了retentionDays；2、制作失败的镜像缓存自动在24小时后过期。 |



CreateImageCache 
-------------------------------------

创建镜像缓存对象。

**Http Request** 



    POST /apis/eci.alibabacloud.com/v1/imagecaches



**curl request example** 



    curl -X POST -H 'Content-Type: application/yaml' --data '
    apiVersion: eci.alibabacloud.com/v1
    kind: ImageCache
    metadata:
      name: imagecache-secrets-test
    spec:
      images:
      - registry.cn-shanghai.aliyuncs.com/baz/nginx:1.0
      imagePullSecrets:
      - default:acr-test
    ' http://127.0.0.1:8001/apis/eci.alibabacloud.com/v1/imagecaches



**curl response example** 



    {
      "apiVersion": "eci.alibabacloud.com/v1",
      "kind": "ImageCache",
      "metadata": {
        "creationTimestamp": "2019-10-10T03:50:47Z",
        "generation": 1,
        "name": "imagecache-secrets-test",
        "resourceVersion": "647052328",
        "selfLink": "/apis/eci.alibabacloud.com/v1/imagecaches/imagecache-secrets-test11",
        "uid": "2506ba37-eb11-11e9-9576-f2ef49bf1a08"
      },
      "spec": {
        "imagePullSecrets": [
          "default:acr-test"
        ],
        "images": [
          "registry.cn-shanghai.aliyuncs.com/baz/nginx:1.0"
        ]
      }
    }



ListImageCache 
-----------------------------------

获取镜像缓存列表。

**Http Request** 



    GET /apis/eci.alibabacloud.com/v1/imagecaches



**curl request example** 



    curl -X GET 'http://127.0.0.1:8001/apis/eci.alibabacloud.com/v1/imagecaches'



**curl response example** 



    {
      "apiVersion": "eci.alibabacloud.com/v1",
      "items": [
        {
          "apiVersion": "eci.alibabacloud.com/v1",
          "kind": "ImageCache",
          "metadata": {
            "annotations": {
              "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"eci.alibabacloud.com/v1\",\"kind\":\"ImageCache\",\"metadata\":{\"annotations\":{},\"name\":\"imagecache-secrets-test\"},\"spec\":{\"imagePullSecrets\":[\"default:acr-test\"],\"images\":[\"registry.cn-shanghai.aliyuncs.com/baz/nginx:1.0\"]}}\n"
            },
            "creationTimestamp": "2019-10-10T01:43:48Z",
            "generation": 1,
            "name": "imagecache-secrets-test",
            "resourceVersion": "646375979",
            "selfLink": "/apis/eci.alibabacloud.com/v1/imagecaches/imagecache-secrets-test",
            "uid": "67460461-eaff-11e9-9774-b2c06862f69a"
          },
          "spec": {
            "imagePullSecrets": [
              "default:acr-test"
            ],
            "images": [
              "registry.cn-shanghai.aliyuncs.com/baz/nginx:1.0"
            ]
          },
          "status": {
            "imageCacheId": "imc-2ze5xnx4yiply807zo0o",
            "lastUpdatedTime": "2019-10-10T01:47:14Z",
            "phase": "Ready",
            "progress": "100%",
            "startTime": "2019-10-10T01:43:49Z"
          }
        }
      ],
      "kind": "ImageCacheList",
      "metadata": {
        "continue": "",
        "resourceVersion": "647062964",
        "selfLink": "/apis/eci.alibabacloud.com/v1/imagecaches"
      }
    }



GetImageCache 
----------------------------------

获取某一个镜像缓存的相信信息。

**Http Request** 



    GET /apis/eci.alibabacloud.com/v1/imagecaches/{name}



**curl request example** 



    curl -X GET 'http://127.0.0.1:8001/apis/eci.alibabacloud.com/v1/imagecaches/imagecache-secrets-test'



**curl response example** 



    {
      "apiVersion": "eci.alibabacloud.com/v1",
      "kind": "ImageCache",
      "metadata": {
        "annotations": {
          "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"eci.alibabacloud.com/v1\",\"kind\":\"ImageCache\",\"metadata\":{\"annotations\":{},\"name\":\"imagecache-secrets-test\"},\"spec\":{\"imagePullSecrets\":[\"default:acr-test\"],\"images\":[\"registry.cn-shanghai.aliyuncs.com/baz/nginx:1.0\"]}}\n"
        },
        "creationTimestamp": "2019-10-10T01:43:48Z",
        "generation": 1,
        "name": "imagecache-secrets-test",
        "resourceVersion": "646375979",
        "selfLink": "/apis/eci.alibabacloud.com/v1/imagecaches/imagecache-secrets-test",
        "uid": "67460461-eaff-11e9-9774-b2c06862f69a"
      },
      "spec": {
        "imagePullSecrets": [
          "default:acr-test"
        ],
        "images": [
          "registry.cn-shanghai.aliyuncs.com/baz/nginx:1.0"
        ]
      },
      "status": {
        "imageCacheId": "imc-2ze5xnx4yiply807zo0o",
        "lastUpdatedTime": "2019-10-10T01:47:14Z",
        "phase": "Ready",
        "progress": "100%",
        "startTime": "2019-10-10T01:43:49Z"
      }
    }
                            



DeleteImageCache 
-------------------------------------

删除指定镜像缓存。

**Http Request** 



    DELETE /apis/eci.alibabacloud.com/v1/imagecaches/{name}



**curl request example** 



    curl -X DELETE -H 'Content-Type: application/yaml' 'http://127.0.0.1:8001/apis/eci.alibabacloud.com/v1/imagecaches/imagecache-secrets-test'



**curl response example** 



      "kind": "Status",
      "apiVersion": "v1",
      "metadata": {},
      "status": "Success",
      "details": {
        "name": "imagecache-secrets-test",
        "group": "eci.alibabacloud.com",
        "kind": "imagecaches",
        "uid": "67460461-eaff-11e9-9774-b2c06862f69a"
      }
    }        



