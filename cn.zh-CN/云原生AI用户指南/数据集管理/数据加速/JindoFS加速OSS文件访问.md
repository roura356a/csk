---
keyword: [JindoFS加速OSS文件访问, 数据加速, JindoRuntime]
---

# JindoFS加速OSS文件访问

JindoRuntime来源于阿里云EMR团队JindoFS，是基于C++实现的支撑Dataset数据管理和缓存的执行引擎，支持OSS对象存储。JindoFS是阿里云的产品，有专门的产品级支持。Fluid通过管理和调度JindoRuntime实现数据集的可见性、弹性伸缩和数据迁移。本文介绍JindoFS如何加速OSS文件访问。

-   在Kubernetes集群中已安装好Fluid，且版本大于等于0.5.0。具体操作，请参见[Fluid](https://github.com/aliyun/alibabacloud-jindofs/blob/master/docs/jindo_fluid/jindo_fluid_introduce.md)。
-   已创建ACK Pro版集群，且集群的Kubernetes版本不低于1.14。具体操作步骤，请参见[创建ACK Pro版集群](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。
-   通过kubectl连接Kubernetes集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。
-   已开通阿里云对象存储（OSS）服务。具体操作，请参见[开通OSS服务](/cn.zh-CN/控制台用户指南/开通OSS服务.md)。

在准备好基本K8s和OSS环境的条件下，您只需要耗费10分钟左右即可部完成JindoRuntime环境的部署。

## 步骤一：准备OSS Bucket的数据

1.  执行以下命令，下载一份测试数据。

    ```
    wget https://archive.apache.org/dist/spark/spark-3.0.1/spark-3.0.1-bin-hadoop2.7.tgz
    ```

2.  将下载的测试数据上传到[阿里云OSS](https://cn.aliyun.com/product/oss)对应的Bucket上，上传方法可以借助OSS提供的客户端工具**ossutil**。具体操作，请参见[下载和安装](/cn.zh-CN/常用工具/命令行工具ossutil/下载和安装.md)。


## 步骤二：创建Dataset和JindoRuntime

1.  在创建Dataset之前，您可以创建一个mySecret.yaml文件来保存OSS的fs.oss.accessKeyId和fs.oss.accessKeySecret

    创建mySecret.yaml文件的YAML样例如下：

    ```
    apiVersion: v1
    kind: Secret
    metadata:
      name: mysecret
    stringData:
      fs.oss.accessKeyId: xxx
      fs.oss.accessKeySecret: xxx
    ```

2.  执行以下命令，生成Secret。

    ```
    kubectl create -f mySecret.yaml
    ```

    K8s会对已创建的Secret使用加密编码，避免将其明文暴露。

3.  使用以下YAML文件样例创建一个名为resource.yaml的文件，且里面包含两部分：

    -   创建一个Dataset，描述远端存储数据集和UFS的信息。
    -   创建一个JindoRuntime，启动一个JindoFS的集群来提供缓存服务。
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

    相关参数解释如下表所示：

    |参数|说明|
    |--|--|
    |**mountPoint**|oss://<oss\_bucket\>/<bucket\_dir\>表示挂载UFS的路径，路径中不需要包含endpoint信息。|
    |**fs.oss.endpoint**|OSS Bucket的endpoint信息，公网或私网地址皆可。|
    |**replicas**|表示创建JindoFS集群的Worker数量。|
    |**mediumtype**|表示缓存类型。定义创建JindoRuntime模板样例时，JindoFS暂时只支持HDD/SSD/MEM中的其中一种缓存类型。|
    |**path**|表示存储路径，暂时只支持单个路径。当选择MEM做缓存时，需指定一个本地路径来存储Log等文件。|
    |**quota**|表示缓存最大容量，单位GB。|
    |**high**|表示存储容量上限大小。|
    |**low**|表示存储容量下限大小。|

4.  执行以下命令，创建JindoRuntime和Dataset。

    ```
    kubectl create -f resource.yaml
    ```

5.  执行以下命令，查看Dataset的部署情况。

    ```
    kubectl get dataset hadoop
    ```

    预期输出：

    ```
    NAME     UFS TOTAL SIZE   CACHED   CACHE CAPACITY   CACHED PERCENTAGE   PHASE   AGE
    hadoop        210MiB       0.00B    180.00GiB              0.0%          Bound   1h
    ```

6.  执行以下命令，查看JindoRuntime的部署情况。

    ```
    kubectl get jindoruntime hadoop
    ```

    预期输出：

    ```
    NAME     MASTER PHASE   WORKER PHASE   FUSE PHASE   AGE
    imagenet   Ready          Ready          Ready        4m45s
    ```

7.  执行以下命令，查看PV和PVC的创建情况。

    ```
    kubectl get pv,pvc
    ```

    预期输出：

    ```
    NAME                      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM            STORAGECLASS   REASON   AGE
    persistentvolume/imagenet   100Gi      RWX            Retain           Bound    default/imagenet                           52m
    
    NAME                           STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
    persistentvolumeclaim/imagenet   Bound    imagenet   100Gi      RWX                           52m
    ```


从上述输出的查询信息，可以知道Dataset和JindoRuntime已创建成功。

## 步骤三：创建应用容器体验加速效果

您可以通过创建应用容器来使用JindoFS加速服务，或者提交机器学习作业来体验相关功能。本文以创建一个应用容器多次访问同一数据，并通过比较访问时间来展示JindoRuntime的加速效果。

1.  使用以下YAML文件样例，创建名为app.yaml 的文件。

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

2.  执行以下命令，创建应用容器。

    ```
    kubectl create -f app.yaml
    ```

3.  执行以下命令，查看文件大小。

    ```
    kubectl exec -it demo-app -- bash
    du -sh /data/hadoop/spark-3.0.1-bin-hadoop2.7.tgz 
    ```

    预期输出：

    ```
    210M    /data/hadoop/spark-3.0.1-bin-hadoop2.7.tgz
    ```

4.  执行如下命令，查看文件的拷贝时间。

    ```
    time cp /data/hadoop/spark-3.0.1-bin-hadoop2.7.tgz /dev/null
    ```

    预期输出：

    ```
    real    0m18.386s
    user    0m0.002s
    sys    0m0.105s
    ```

    从上述输出信息，可以知道文件拷贝时间消耗了18s。

5.  执行以下命令，查看此时Dataset的缓存情况。

    ```
    kubectl get dataset hadoop
    ```

    预期输出：

    ```
    NAME     UFS TOTAL SIZE   CACHED   CACHE CAPACITY   CACHED PERCENTAGE   PHASE   AGE
    hadoop   210.00MiB       210.00MiB    180.00GiB        100.0%           Bound   1h
    ```

    从上述输出信息，可以知道210 MiB的数据已经都缓存到了本地。

6.  执行以下命令，删除之前的应用容器，新建相同的应用容器。

    **说明：** 这样做的目的是为了避免其他因素（比如：Page Cache）对结果造成影响。

    ```
    kubectl delete -f app.yaml && kubectl create -f app.yaml
    ```

7.  执行如下命令，查看文件拷贝时间。

    ```
    kubectl exec -it demo-app -- bash
    time cp /data/hadoop/spark-3.0.1-bin-hadoop2.7.tgz /dev/null
    ```

    预期输出：

    ```
    real    0m0.048s
    user    0m0.001s
    sys    0m0.046s
    ```

    从上述输出信息，可以知道进行文件的cp拷贝观察时间消耗48 ms，整个拷贝的时间缩短了300多倍。

    **说明：** 由于文件已经被JindoFS缓存，第二次访问所需时间远小于第一次。


## 环境清理

当您不再使用该数据加速功能时，需要清理环境。

执行以下命令，删除JindoRuntime和应用容器。

```
kubectl delete jindoruntime hadoop
```

执行以下命令，删除Dataset。

```
kubectl delete dataset hadoop
```

