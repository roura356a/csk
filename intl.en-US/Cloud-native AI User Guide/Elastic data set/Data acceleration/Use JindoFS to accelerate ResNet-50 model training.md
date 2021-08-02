---
keyword: [use JindoFS to accelerate ResNet-50 model training, data acceleration, JindoRuntime]
---

# Use JindoFS to accelerate ResNet-50 model training

JindoRuntime is the execution engine of JindoFS developed by the Alibaba Cloud E-MapReduce \(EMR\) team. JindoRuntime is based on C++ and provides dataset management and caching. JindoRuntime also supports Object Storage Service \(OSS\). Fluid enables the observability, scalability, and portability of datasets by managing and scheduling JindoRuntime. This topic describes how to use Fluid to deploy ImageNet datasets from OSS to a Kubernetes cluster. It also describes how to use Arena to train ResNet-50 models based on the deployed datasets.

-   A professional Kubernetes cluster of Kubernetes version 1.14 or later is created. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).
-   A kubectl client is connected to the created cluster. For more information, see [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).
-   Fluid 0.5.0 or later is installed in the created cluster. For more information, see [Fluid](https://github.com/fluid-cloudnative/fluid).
-   Arena 0.6.0 or later is installed. For more information, see [Arena](https://github.com/kubeflow/arena).
-   The code of Horovod 0.18.1 or later is obtained. For more information, see [Horovod](https://github.com/horovod/horovod).
-   The code of TensorFlow benchmarks is obtained. For more information, see [Benchmark](https://github.com/tensorflow/benchmarks/tree/cnn_tf_v1.14_compatible).

## Use Fluid to deploy ImageNet datasets from OSS to a Kubernetes cluster

**Note:**

-   If you do not want to use the datasets provided by Alibaba Cloud, you can download datasets from the official website of ImageNet. For more information, see [images](http://image-net.org/download-images).
-   If you want to use the datasets provided by Alibaba Cloud, submit an issue to Fluid to request the datasets. For more information, see [Fluid](https://github.com/fluid-cloudnative/fluid).

In this example, a training job that runs on four nodes provided by Alibaba Cloud with eight NVIDIA Tesla V100 GPUs is used to show how to use Fluid to deploy ImageNet datasets from OSS to a Kubernetes cluster.

1.  Create a file named dataset.yaml and copy the following content into the file:

    ```
    apiVersion: v1
    kind: Secret
    metadata:
      name: mysecret
    stringData:
      fs.oss.accessKeyId: xxx
      fs.oss.accessKeySecret: xxx
    ---
    apiVersion: data.fluid.io/v1alpha1
    kind: Dataset
    metadata:
      name: imagenet
    spec:
      mounts:
        - mountPoint: oss://<oss_bucket>/<bucket_dir>
          options:
            fs.oss.endpoint: <oss_endpoint>  
          name: imagenet
          encryptOptions:
            - name: fs.oss.accessKeyId
              valueFrom:
                secretKeyRef:
                  name: mysecret
                  key: fs.oss.accessKeyId
            - name: fs.oss.accessKeySecret
              valueFrom:
                secretKeyRef:
                  name: mysecret
                  key: fs.oss.accessKeySecret
      nodeAffinity:   # The nodeAffinity settings ensure that the data is cached to nodes with NVIDIA Tesla V100 GPUs. 
        required:
          nodeSelectorTerms:
            - matchExpressions:
                - key: aliyun.accelerator/nvidia_name
                  operator: In
                  values:
                    - Tesla-V100-SXM2-16GB
    ```

    To ensure that the datasets are mounted to JindoFS from [OSS](https://www.alibabacloud.com/zh/product/oss), you must set mountPoint, fs.oss.accessKeyId, fs.oss.accessKeySecret, and fs.oss.endpoint to correct values. For more information about the parameters, see [t2057532.md\#table\_0wq\_ba5\_em0](/intl.en-US/Cloud-native AI User Guide/Elastic data set/Data acceleration/Use JindoFS to accelerate access to OSS.md).

2.  Create a file named runtime and copy the following content into the file:

    ```
    apiVersion: data.fluid.io/v1alpha1
    kind: JindoRuntime
    metadata:
      name: imagenet
    spec:
      replicas: 4
      tieredstore:
        levels:
          - mediumtype: SSD
            path: /var/lib/docker/jindo
            quota: 180G
            high: "0.99"
            low: "0.8"
    ```

    In this example, the training job runs on four nodes with eight NVIDIA Tesla V100 GPUs. Therefore, `spec.replicas` is set to 4.

3.  Run the following commands to create a dataset and a JindoRuntime:

    ```
    kubectl create -f dataset.yaml
    kubectl create -f runtime.yaml
    ```

4.  Run the following command to check whether the dataset is created:

    ```
    kubectl get dataset
    ```

    Expected output:

    ```
    NAME     UFS TOTAL SIZE   CACHED   CACHE CAPACITY   CACHED PERCENTAGE   PHASE   AGE
    imagenet   143.67GiB        0.00B    648.00GiB        0.0%                Bound   5
    ```

5.  Run the following command to check whether the JindoRuntime is created:

    ```
    kubectl get jindoruntime
    ```

    Expected output:

    ```
    NAME     MASTER PHASE   WORKER PHASE   FUSE PHASE   AGE
    imagenet   Ready          Ready          Ready        4m45s
    ```

6.  Run the following command to check whether the persistent volume \(PV\) and persistent volume claim \(PVC\) are created:

    ```
    kubectl get pv,pvc
    ```

    Expected output:

    ```
    NAME                      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM            STORAGECLASS   REASON   AGE
    persistentvolume/imagenet   100Gi      RWX            Retain           Bound    default/imagenet                           52m
    
    NAME                           STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
    persistentvolumeclaim/imagenet   Bound    imagenet   100Gi      RWX                           52m
    ```


The preceding outputs indicate that the ImageNet datasets are deployed to the Kubernetes cluster.

## Use Arena to submit a deep learning job

Run the following command to submit a ResNet-50 model training job that runs on four nodes with eight NVIDIA Tesla V100 GPUs:

```
arena submit mpi \
--name horovod-resnet50 \
--gpus=8 \
--workers=4 \
--working-dir=/horovod-demo/tensorflow-demo/ \
--data imagenet:/data \
-e DATA_DIR=/data/imagenet \
-e num_batch=1000 \
-e datasets_num_private_threads=8 \
--image=registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/horovod-benchmark-dawnbench-v2:0.18.1-tf1.14.0-torch1.2.0-mxnet1.5.0-py3.6 \
./launch-example.sh 4 8
```

Description of Arena parameters:

|Parameter|Description|
|---------|-----------|
|**--name**|Specifies the name of the training job.|
|**--workers**|Specifies the number of worker nodes on which the job runs.|
|**--gpus**|Specifies the number of GPUs that are used by each worker node on which the job runs.|
|**--working-dir**|Specifies the working directory.|
|**--data**|Mounts the `imagenet` PV to the /data directory of the node.|
|**-e**|Specifies the path to the dataset.|
|**launch-example.sh 4 8**|Executes the script to start the job.|

**Check whether the job is started**

Run the following command to check whether the job is started:

```
arena get horovod-resnet50            
```

Expected output:

```
STATUS: RUNNING
NAMESPACE: default
PRIORITY: N/A
TRAINING DURATION: 56m

NAME              STATUS   TRAINER  AGE  INSTANCE                         NODE
horovod-resnet50  RUNNING  MPIJOB   56m  horovod-resnet50-launcher-xs7h4  192.168.0.15
horovod-resnet50  RUNNING  MPIJOB   56m  horovod-resnet50-worker-0        192.168.0.15
horovod-resnet50  RUNNING  MPIJOB   56m  horovod-resnet50-worker-1        192.168.0.14
horovod-resnet50  RUNNING  MPIJOB   56m  horovod-resnet50-worker-2        192.168.0.12
horovod-resnet50  RUNNING  MPIJOB   56m  horovod-resnet50-worker-3        192.168.0.13
```

The output shows that the ResNet-50 model training job is started.

**Check the log of Arena**

Run the following command to check the log of Arena:

```
arena logs --tail 100 -f horovod-resnet50
```

## Performance comparison

In this topic, Fluid is used to deploy ImageNet datasets from OSS to a Kubernetes cluster, Arena is used to run a ResNet-50 model training job based on the deployed datasets, and on-premises caching is enabled based on JindoRuntime of JindoFS. This method provides higher performance than using OSSFS and reduces the time consumption by 76%.

## Clear the environment

If you no longer use data acceleration, clear the environment.

Run the following command to delete the dataset and JindoRuntime:

```
kubectl delete -f dataset imagenet
kubectl delete -f jindoruntime imagenet
```

