Use an image cache CRD to accelerate pod creation 
======================================================================

Downloading images is time-consuming when you create an Elastic Container Instance (ECI). ECI allows you to use an image cache to accelerate ECI creation. Before you create an ECI, you can cache the required images as an image snapshot on a disk. Then, ECI creates the ECI based on the image snapshot to download fewer or no images. This accelerates the creation of the ECI. Assume that you create an ECI based on Flink images from Docker Hub. The total size of the images is 386.26 MB. During testing, downloading images without using an image snapshot takes `50s`, whereas downloading images by using an image snapshot only takes `5s`. Using an image snapshot saves `45s` when you create an ECI. The saved time depends on the number and size of images used to create an ECI and the network over which ECI connects to the image repository.

ECI provides an image cache custom resource definition (CRD) for you to use the image cache feature in Kubernetes. Note that the image cache CRD is a **cluster-level** resource and is similar to a persistent volume (PV). The image cache CRD can be shared by all namespaces in a cluster.

Before you begin 
-------------------------------------

Verify that your Kubernetes cluster supports the image cache feature.

    $ kubectl get crd/imagecaches.eci.alibabacloud.com
    NAME                                                CREATED AT
    imagecaches.eci.alibabacloud.com   2019-09-27T01:15:07Z



If the preceding information is returned, your Kubernetes cluster supports the image cache feature.

    kubectl get crd/imagecaches.eci.alibabacloud.com
    Error from server (NotFound): customresourcedefinitions.apiextensions.k8s.io "imagecaches.eci.alibabacloud.com" not found



If the preceding information is returned, your Kubernetes cluster does not support the image cache feature. Upgrade `Virtual Kubelet` to enable the image cache feature.

The user who is responsible for upgrading Virtual Kubelet varies according to the cluster type.

* Serverless Kubernetes cluster: The administrator upgrades Virtual Kubelet.

* Managed Kubernetes cluster: You need to upgrade Virtual Kubelet by yourself.

* Dedicated Kubernetes cluster: You need to upgrade Virtual Kubelet by yourself.

* User-created Kubernetes cluster: You need to upgrade Virtual Kubelet by yourself.




After you verify that the image cache feature is supported, you can create image caches in your Kubernetes cluster and use them to create pods.

To use the newly added parameters, you must upgrade the image cache CRD in your Kubernetes cluster to the latest version. We recommend that you run the following command after Virtual Kubelet is upgraded:

    kubectl apply -f imagecache-crd-sample.yaml



imagecache-crd-sample.yaml:

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



Manage an image cache 
------------------------------------------

After the image cache CRD is upgraded to the latest version, you can manage image caches.

**YAML file of an image cache:** 

    apiVersion: eci.alibabacloud.com/v1
    kind: ImageCache
    metadata:
      name: imagecache-sample
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
    			



**Parameter description** 


|       Parameter       |    Type    | Required |                                                                                                                                                                           Description                                                                                                                                                                            |
|-----------------------|------------|----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| spec.images           | \[\]String | Yes      | The images in the image cache.                                                                                                                                                                                                                                                                                                                                   |
| spec.imagePullSecrets | \[\]String | No       | The secrets of the image repositories. Specify each secret in the **namespace:secretName** format. If the image cache contains images from a private image repository, you must create a secret for the repository and add the secret to the value of this parameter. If all images in the image cache are public images, you do not need to set this parameter. |
| spec.imageCacheSize   | Integer    | No       | The size of the disk used to store image snapshots. Valid values: 20 to 32768. Default value: 20. Unit: GiB.                                                                                                                                                                                                                                                     |
| spec.retentionDays    | Integer    | No       | The retention period of the image cache. Valid values: 1 to 65536. By default, the parameter is not specified, indicating that the image cache does not expire.                                                                                                                                                                                                  |



Create an image cache 
------------------------------------------

Create an image cache.

    # kubectl create -f  imagecache-secrets-test.yaml
    apiVersion: eci.alibabacloud.com/v1
    kind: ImageCache
    metadata:
      name: imagecache-sample-liu
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



View the creation status of the image cache.

    liumihustdeMacBook-Pro:vk-debug liumihust$ kubectl get imagecache imagecache-sample-liu
    NAME                              AGE   CACHEID                               PHASE   PROGRESS
    imagecache-sample-liu   20h   imc-2zeditzeoemfhqor4rf8   Ready    100%
    			



Query image caches 
---------------------------------------

You can run the following command to query all image caches in the Kubernetes cluster:

    kubectl get imagecache imagecache-sample-liu -o yaml



You can view the details of an image cache, including its properties, status, and events through the following YAML file:

    $ kubectl get imagecache/imagecache-secrets-test -o yaml
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



Quickly create a pod based on an image cache 
-----------------------------------------------------------------

An image cache is a cluster-level resource. You can use it to create pods in different namespaces.

You can use an image cache to create a pod in one of the following ways:

* Specify an image cache. When you create a pod, you can specify an image cache.

* Enable automatic image cache match. When you create a pod, ECI automatically selects the optimal image cache based on matching rules.




**Specify an image cache** 

You must specify an image cache in the `Ready` state to accelerate pod creation. Otherwise, the pod fails to be created.

**Specify an image cache in the YAML file of a deployment** 

Add an annotation in the YAML file of a deployment to specify an image cache.

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



**Specify an image cache in the YAML file of a pod** 

Add an annotation in the YAML file of a pod to specify an image cache.

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



**Enable automatic image cache match** 

You can enable automatic image cache match through an annotation to accelerate pod creation. ECI selects the optimal image cache from existing image caches based on matching rules. If no suitable image cache is found, ECI creates a pod by downloading required images.

The following matching rules are listed based on their priorities in descending order:

* The matching degree of the images.

* The size of the images.

* The time when the image cache was created.




**Enable automatic image cache match in the YAML file of a deployment** 

Add an annotation in the YAML file of a deployment to enable automatic image cache match.

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



**Enable automatic image cache match in the YAML file of a pod** 

Add an annotation in the YAML file of a pod to enable automatic image cache match.

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



Recommended configurations 
-----------------------------------------------

1. We recommend that you specify an image in the image cache for a container to improve the matching degree.

2. We recommend that you set the `ImagePullPolicy` parameter to `IfNotPresent` for a container to avoid downloading duplicate images.




If you add the `k8s.aliyun.com/eci-image-snapshot-id` and `k8s.aliyun.com/eci-image-cache` annotations at the same time, the former takes precedence over the latter. The specified image cache is used to create a pod.

**Notice** In the YAML file of a deployment, you must add the annotation for a pod. If you add the annotation for the deployment, the annotation does not take effect.

Delete an image cache 
------------------------------------------

If you delete an image cache, the pods created based on the image cache are not affected.

Run the following commands to delete an image cache:

    $ kubectl get imagecache/imagecache-secrets-test
    NAME                                AGE   CACHEID                                 PHASE   PROGRESS
    imagecache-secrets-test   14h   imc-2ze5tm5gehgtjru0kfm8   Ready    100%
    $ kubectl delete imagecache/imagecache-secrets-test
    imagecache.eci.alibabacloud.com " imagecache-secrets-test" deleted
    $ kubectl get imagecache/ imagecache-secrets-test
    Error from server (NotFound): imagecaches.eci.alibabacloud.com " imagecache-secrets-test" not found
    			



API reference related to the image cache feature in Kubernetes 
-----------------------------------------------------------------------------------



|        Group         | Version |    Kind    |
|----------------------|---------|------------|
| eci.alibabacloud.com | v1      | ImageCache |



ImageCache v1 eci.alibabacloud.com 
-------------------------------------------------------

**ImageCacheList** 


| Parameter  |                                                         Type                                                         |                            Description                            |
|------------|----------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| apiVersion | String                                                                                                               | The version of the API. Set the value to eci.alibabacloud.com/v1. |
| kind       | String                                                                                                               | The type of the resource. Set the value to ImageCacheList.        |
| metadata   | [ListMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#listmeta-v1-meta) | The metadata that the resource must have.                         |
| Items      | \[\]ImageCache                                                                                                       | The image caches.                                                 |



**ImageCache** 


| Parameter  |       Type       |                            Description                            |
|------------|------------------|-------------------------------------------------------------------|
| apiVersion | String           | The version of the API. Set the value to eci.alibabacloud.com/v1. |
| kind       | String           | The type of the resource. Set the value to ImageCache.            |
| metadata   | [ObjectMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#objectmeta-v1-meta) ||
| spec       | ImageCacheSpec   | The details of the image cache.                                   |
| status     | ImageCacheStatus | The status of the image cache.                                    |



**ImageCacheSpec** 


|    Parameter     |    Type    | Required |                                                                           Description                                                                           |
|------------------|------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| images           | \[\]String | Yes      | The images in the image cache.                                                                                                                                  |
| imagePullSecrets | \[\]String | No       | The secrets of the private image repositories. Specify each secret in the **namespace:secretName** format.                                                      |
| imageCacheSize   | Integer    | No       | The size of the image cache. Valid values: 20 to 32768. Default value: 20. Unit: GiB.                                                                           |
| retentionDays    | Integer    | No       | The retention period of the image cache. Valid values: 1 to 65536. By default, the parameter is not specified, indicating that the image cache does not expire. |



**ImageCacheStatus** 


|    Parameter    |                                                     Type                                                     |                                                                          Description                                                                           |
|-----------------|--------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| phase           | String                                                                                                       | The status of the image cache.                                                                                                                                 |
| progress        | String                                                                                                       | The creation progress of the image cache.                                                                                                                      |
| imageCacheId    | String                                                                                                       | The ID of the image cache. You can use the image cache ID returned by the CreateImageCache operation.                                                          |
| startTime       | [Time](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#time-v1-meta) | The time when the image cache was created.                                                                                                                     |
| lastUpdatedTime | [Time](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#time-v1-meta) | The most recent time when the image cache was updated.                                                                                                         |
| events          | \[\]Event                                                                                                    | The events generated during the creation of the image cache.                                                                                                   |
| expireDateTime  | [Time](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#time-v1-meta) | The time when the image cache expires. The image cache expires when the specified retention period ends or 24 hours after the image cache fails to be created. |



CreateImageCache 
-------------------------------------

Creates an image cache.

**HTTP request URL** 

`POST /apis/eci.alibabacloud.com/v1/imagecaches`

**Sample request**


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



**Sample response**


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

Queries the image caches in a Kubernetes cluster.

**HTTP request URL** 

`GET /apis/eci.alibabacloud.com/v1/imagecaches`

**Sample request**


    curl -X GET 'http://127.0.0.1:8001/apis/eci.alibabacloud.com/v1/imagecaches'



**Sample response**


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

Queries the details of an image cache.

**HTTP request URL** 

`GET /apis/eci.alibabacloud.com/v1/imagecaches/{name}`

**Sample request**


    curl -X GET 'http://127.0.0.1:8001/apis/eci.alibabacloud.com/v1/imagecaches/imagecache-secrets-test'



**Sample response**


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

Deletes an image cache.

**HTTP request URL** 

`DELETE /apis/eci.alibabacloud.com/v1/imagecaches/{name}`

**Sample request**


    curl -X DELETE -H 'Content-Type: application/yaml' 'http://127.0.0.1:8001/apis/eci.alibabacloud.com/v1/imagecaches/imagecache-secrets-test'



**Sample response**


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



