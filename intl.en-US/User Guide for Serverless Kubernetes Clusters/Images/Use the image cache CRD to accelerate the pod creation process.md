Use the image cache CRD to accelerate the pod creation process 
===================================================================================

Alibaba Cloud provides the image cache feature as a custom resource definition (CRD) to accelerate the speed at which you can create pods in Kubernetes. This topic describes how to use the image cache CRD to accelerate the pod creation process. 

Background information 
-------------------------------------------

When you create an elastic container instance, downloading images is a time-consuming process. Elastic Container Instance provides the image cache feature to accelerate the creation of elastic container instances. Before you create an elastic container instance, you can cache the required images as snapshots. Then, Elastic Container Instance can create elastic container instances based on the cached snapshots. This accelerates the creation of elastic container instances and reduces and even eliminates the need to download images. 

For example, if you want to create an elastic container instance from Flink images on Docker Hub and the total size of the images is 386.26 MB, the download may require 50 seconds. You can cache the images as snapshots. This can reduce the download time from 50 seconds to 5 seconds. 
**Note**

The amount of time saved is based on the number and size of images used to create an elastic container instance and the network over which Elastic Container Instance connects to the image repository.

Alibaba Cloud provides the image cache feature as a CRD for use in Kubernetes. The CRD is named ImageCache. 
**Notice**

An ImageCache CRD is a cluster-level resource and is similar to a persistent volume (PV). The ImageCache CRD of a Kubernetes cluster is shared by all namespaces within the cluster.

Preparations 
---------------------------------

To check whether a Kubernetes cluster supports ImageCache, log on to the cluster and run the following command: 

```unknow
kubectl get crd/imagecaches.eci.alibabacloud.com
```



* If an error is returned, the cluster does not support ImageCache. Example output:

  ```unknow
  Error from server (NotFound): customresourcedefinitions.apiextensions.k8s.io "imagecaches.eci.alibabacloud.com" not found
  ```

  

  If the cluster does not support ImageCache, upgrade Virtual Kubelet to a version that supports ImageCache. 
  **Note**

  We recommend that you upgrade Virtual Kubelet to the latest version to use new features. For more information about Virtual Kubelet versions, see [ack-virtual-node](/intl.en-US/Release notes/System Component change Records/Other SQL statements/ack-virtual-node.md).
  

* If imagecaches.eci.alibabacloud.com is returned, the cluster supports ImageCache. Example output:

  ```unknow
  NAME                                                CREATED AT
  imagecaches.eci.alibabacloud.com                    2019-09-27T01:15:07Z
  ```

  

  If the version of ImageCache CRD in your cluster is not the latest version, we recommend that you run the kubectl apply command to upgrade the CRD to the latest version. 

  ```shell
  kubectl apply -f imagecache-crd-sample.yaml
  ```

  

  Example imagecache-crd-sample.yaml file:

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

  




Manage image caches 
----------------------------------------

After you create the ImageCache CRD, you can manage image caches. For more information, see [Kubernetes ImageCache API](t2059054.html#topic-2059054). 

Example YAML configuration file of the image cache feature:

```yaml
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
```



Parameters


|       Parameter       |    Type    | Required |                                                                                                                                                                                                                Description                                                                                                                                                                                                                 |
|-----------------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| spec.images           | String\[\] | Yes      | The container images used to create the image cache.                                                                                                                                                                                                                                                                                                                                                                                       |
| spec.imagePullSecrets | String\[\] | No       | The secrets of the image repositories.  * To cache images from private image repositories, you must create a secret for the repository in the `namespace:secretName` format and add the secret to the value of this parameter.   * If all the images you want to cache are public images, you do not need to specify this parameter.    |
| spec.imageCacheSize   | int        | No       | The size of the image cache, which is the size of the disk used to create the image cache snapshot. Unit: GB. Default value: 20. Valid values: 20 to 32768.                                                                                                                                                                                                                                                                                |
| spec.retentionDays    | int        | No       | The retention period of the image cache. When the specified retention period ends, the image cache expires and is deleted. Unit: days. By default, this parameter is empty, which indicates that the image cache does not expire.                                                                                                                                                                                                          |



### Create an image cache 

1. Create an image cache. 

   ```shell
   kubectl create -f imagecache-secrets-test.yaml
   ```

   

   Example imagecache-secrets-test.yaml file:

   ```yaml
   apiVersion: eci.alibabacloud.com/v1
   kind: ImageCache
   metadata:
     name: imagecache-sample-test
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

   

2. View the status of the image cache. 

   ```shell
   kubectl get imagecache imagecache-sample-test
   ```

   

   Example output:

   ```shell
   NAME                              AGE   CACHEID                               PHASE   PROGRESS
   imagecache-sample-test            20h   imc-2zeditzeoemfhqor****              Ready    100%
   ```

   




### Query image caches 

You can query details about a specific image cache or all image caches in a specific cluster.

* Query details about all image caches in a specific cluster.

  ```shell
  kubectl get imagecache
  ```

  

* Query details of a specific image cache.

  ```shell
  kubectl get imagecache/imagecache-sample-test -o yaml
  ```

  




### Delete an image cache 

To delete an image cache, run the following command:

```shell
kubectl delete imagecache/imagecache-sample-test
```



Use an image cache to accelerate the pod creation process 
------------------------------------------------------------------------------

An image cache is a cluster-level resource and can be used to accelerate the creation of pods in different namespaces. 

When you create a pod based on an image cache, you can add annotations to metadata in the pod configuration file to specify which image cache to use or enable automatic matching of image caches. Take note of the following annotations:

* k8s.aliyun.com/eci-image-snapshot-id: specifies an image cache.

  

* k8s.aliyun.com/eci-image-cache: specifies whether to enable automatic matching of image caches to select the most suitable image cache based on matching rules.

  



**Note**

If both `k8s.aliyun.com/eci-image-snapshot-id` and `k8s.aliyun.com/eci-image-cache` are configured, k8s.aliyun.com/eci-image-snapshot-id takes precedence over k8s.aliyun.com/eci-image-cache.

When you create a pod from an image cache, take note of the following items:

* We recommend that you specify an image contained in the image cache for a container to improve the matching degree.

  

* We recommend that you set the image pulling policy (ImagePullPolicy) of containers to on-demand (IfNotPresent) to avoid repeated downloads of image layers.

  




### Specify an image cache 

When you create a pod, you can add an annotation to specify an image cache to accelerate the pod creation process. 
**Notice**

Make sure that the specified image cache is in the Ready state. Otherwise, the pod fails to be created.

* Example of how to add an annotation to specify an image cache in the YAML configuration file of a Deployment

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
          k8s.aliyun.com/eci-image-snapshot-id: imc-2ze5tm5gehgtiiga****  #Specify an image cache.
      spec:
        nodeName: virtual-kubelet
        containers:
        - name: nginx
          image: nginx:1.7.9
          imagePullPolicy: IfNotPresent
  ```

  

* Example of how to add an annotation to enable automatic matching of image caches in the YAML configuration file of a pod

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    annotations:
      k8s.aliyun.com/eci-image-snapshot-id: imc-2ze5tm5gehgtiiga****  #Specify an image cache.
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

  




### Enable automatic matching of image caches 

When you create a pod, you can add an annotation to enable automatic matching of image caches to accelerate the pod creation process. Then, Elastic Container Instance selects the most suitable image cache from your image caches based on the matching rules. Images are matched based on the following elements in descending order of priority: the matching degree of images, the size of matched images, and the creation time of the image cache. 
**Note**

If a suitable image cache is not found, Elastic Container Instance creates a pod by downloading the required images.

* Example of how to add an annotation to enable automatic matching of image caches in the YAML configuration file of a Deployment

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
          k8s.aliyun.com/eci-image-cache: "true"   #Enable automatic matching of image caches.
      spec:
        nodeName: virtual-kubelet
        containers:
        - name: nginx
          image: nginx:1.7.9
          imagePullPolicy: IfNotPresent
  ```

  

* Example of how to add an annotation to enable automatic matching of image caches in the YAML configuration file of a pod

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    annotations:
      k8s.aliyun.com/eci-image-cache: "true"  #Enable automatic matching of image caches.
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

  



