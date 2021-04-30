---
keyword: [use JindoFS to accelerate access to OSS, data acceleration, JIndoRuntime]
---

# Use JindoFS to accelerate access to OSS

JindoRuntime is the execution engine of JindoFS developed by the Alibaba Cloud E-MapReduce \(EMR\) team. JindoRuntime is based on C++ and provides dataset management and caching. JindoRuntime also supports Object Storage Service \(OSS\). Alibaba Cloud provides cloud service-level support for JindoFS. Fluid enables the observability, auto scaling, and portability of datasets by managing and scheduling JindoRuntime. This topic describes how to use JindoFS to accelerate access to Object Storage Service \(OSS\).

-   Fluid 0.5.0 or later is installed in your Kubernetes cluster. For more information, see [Fluid](https://github.com/aliyun/alibabacloud-jindofs/blob/master/docs/jindo_fluid/jindo_fluid_introduce.md).
-   A professional Kubernetes cluster of Kubernetes 1.14 or later is created. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).
-   A kubectl client is connected to the created cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).
-   OSS is activated. For more information, see [Activate OSS](/intl.en-US/Console User Guide/Sign up for OSS.md).

After the Kubernetes cluster is created and OSS is activated, it requires only about 10 minutes to set up the environment for JindoRuntime.

## Step 1: Upload data to OSS

1.  Run the following command to download the test data:

    ```
    wget https://archive.apache.org/dist/spark/spark-3.0.1/spark-3.0.1-bin-hadoop2.7.tgz
    ```

2.  Upload the downloaded data tothe [OSS](https://www.alibabacloud.com/zh/product/oss) bucket that you want to use. You can use the **ossutil** client to upload the data. For more information, see [Download and installation](/intl.en-US/Tools/ossutil/Download and installation.md).


## Step 2: Create a dataset and a JindoRuntime

1.  Before you create a dataset, create a file named mySecret.yaml to use fs.oss.accessKeyId and fs.oss.accessKeySecret to store the AccessKey pair of the OSS bucket.

    Create the mySecret.yaml file by using the following YAML template:

    ```
    apiVersion: v1
    kind: Secret
    metadata:
      name: mysecret
    stringData:
      fs.oss.accessKeyId: xxx
      fs.oss.accessKeySecret: xxx
    ```

2.  Run the following command to create a Secret:

    ```
    kubectl create -f mySecret.yaml
    ```

    Kubernetes encodes and encrypts the created Secret to prevent the stored information from being exposed as plaintext.

3.  Create a file named resource.yaml by using the following YAML template. This template is used to perform the following operations:

    -   Create a dataset to specify information about the datasets in remote storage and the underlying file system \(UFS\).
    -   Create a JindoRuntime to enable JindoFS for data caching in the cluster.
    ```
    apiVersion: data.fluid.io/v1alpha1
    kind: Dataset
    metadata:
      name: hadoop
    spec:
      mounts:
        - mountPoint: oss://<oss_bucket>/<bucket_dir>
          options:
            fs.oss.endpoint: <oss_endpoint>  
          name: hadoop
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
    ---
    apiVersion: data.fluid.io/v1alpha1
    kind: JindoRuntime
    metadata:
      name: hadoop
    spec:
      replicas: 2
      tieredstore:
        levels:
          - mediumtype: HDD
            path: /mnt/disk1
            quota: 100G
            high: "0.99"
            low: "0.8"
    ```

    The following table describes the parameters in the YAML template.

    |Parameter|Description|
    |---------|-----------|
    |**mountPoint**|oss://<oss\_bucket\>/<bucket\_dir\> specifies the path to the UFS that is mounted. The endpoint is not required in the path.|
    |**fs.oss.endpoint**|The public or internal endpoint of the OSS bucket.|
    |**replicas**|The number of worker nodes in the Kubernetes cluster for which JindoFS is enabled.|
    |**mediumtype**|The cache type. This parameter specifies the cache type used when you create JindoRuntime templates. Valid values: HDD, SDD, and MEM.|
    |**path**|The storage path. You can specify only one path. If you set mediumtype to MEM, you must specify a path of the on-premises storage to store data, such as log.|
    |**quota**|The maximum size of cached data. Unit: GB.|
    |**high**|The upper limit of the storage capacity.|
    |**low**|The lower limit of the storage capacity.|

4.  Run the following command to create a dataset and a JindoRuntime:

    ```
    kubectl create -f resource.yaml
    ```

5.  Run the following command to check whether the dataset is created:

    ```
    kubectl get dataset hadoop
    ```

    Expected output:

    ```
    NAME     UFS TOTAL SIZE   CACHED   CACHE CAPACITY   CACHED PERCENTAGE   PHASE   AGE
    hadoop        210MiB       0.00B    180.00GiB              0.0%          Bound   1h
    ```

6.  Run the following command to check whether the JindoRuntime is created:

    ```
    kubectl get jindoruntime hadoop
    ```

    Expected output:

    ```
    NAME     MASTER PHASE   WORKER PHASE   FUSE PHASE   AGE
    imagenet   Ready          Ready          Ready        4m45s
    ```

7.  Run the following command to check whether the persistent volume \(PV\) and persistent volume claim \(PVC\) are created:

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


The preceding outputs indicate that the dataset and JindoRuntime are created.

## Step 3: Create applications to test data acceleration

You can deploy applications in containers to test data acceleration of JindoFS. You can also submit machine learning jobs to use relevant features. In this topic, an application is deployed in a container to test access to the same data. The test is run multiple times to compare the time consumption

1.  Create a file named app.yaml by using the following YAML template:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: demo-app
    spec:
      containers:
        - name: demo
          image: nginx
          volumeMounts:
            - mountPath: /data
              name: hadoop
      volumes:
        - name: hadoop
          persistentVolumeClaim:
            claimName: hadoop
    ```

2.  Run the following command to deploy the application:

    ```
    kubectl create -f app.yaml
    ```

3.  Run the following command to query the size of the specified file:

    ```
    kubectl exec -it demo-app -- bash
    du -sh /data/hadoop/spark-3.0.1-bin-hadoop2.7.tgz 
    ```

    Expected output:

    ```
    210M    /data/hadoop/spark-3.0.1-bin-hadoop2.7.tgz
    ```

4.  Run the following command to query the time required to copy the specified file:

    ```
    time cp /data/hadoop/spark-3.0.1-bin-hadoop2.7.tgz /dev/null
    ```

    Expected output:

    ```
    real    0m18.386s
    user    0m0.002s
    sys    0m0.105s
    ```

    The output indicates that 18 seconds is required to copy the file.

5.  Run the following command to check the cached data of the dataset:

    ```
    kubectl get dataset hadoop
    ```

    Expected output:

    ```
    NAME     UFS TOTAL SIZE   CACHED   CACHE CAPACITY   CACHED PERCENTAGE   PHASE   AGE
    hadoop   210.00MiB       210.00MiB    180.00GiB        100.0%           Bound   1h
    ```

    The output indicates that 210 MiB of data is cached to the on-premises storage.

6.  Run the following command to delete and redeploy the application:

    **Note:** This is to avoid other factors, such as the page cache, from affecting the result.

    ```
    kubectl delete -f app.yaml && kubectl create -f app.yaml
    ```

7.  Run the following command to query the time required to copy the specified file:

    ```
    kubectl exec -it demo-app -- bash
    time cp /data/hadoop/spark-3.0.1-bin-hadoop2.7.tgz /dev/null
    ```

    Expected output:

    ```
    real    0m0.048s
    user    0m0.001s
    sys    0m0.046s
    ```

    The output indicates that 48 milliseconds is required to copy the file. This means that the time consumption is reduced by more than 300 times.

    **Note:** This is because the file is cached by JindoFS.


## Clear the environment

If you no longer use data acceleration, clear the environment.

Run the following command to delete the JindoRuntime and application:

```
kubectl delete jindoruntime hadoop
```

Run the following command to delete the dataset:

```
kubectl delete dataset hadoop
```

