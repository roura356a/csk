---
keyword: [静态云盘, 持久化存储, CSI]
---

# 使用云盘静态存储卷实现持久化存储-CSI

当容器发生宕机故障时，有状态服务容器存储的业务数据存在着丢失和不可靠等风险。使用持久化存储可以解决该问题。本文介绍如何使用云盘静态存储卷实现持久化存储。

-   [创建云盘](/cn.zh-CN/块存储/云盘基础操作/创建云盘/创建云盘.md)
-   [创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)

云盘的使用场景包括：

-   对磁盘I/O要求高的应用，且没有共享数据的需求，如MySQL、Redis等数据存储服务。
-   高速写日志。
-   持久化存储数据，不会因Pod生命周期的结束而消失。

静态云盘的使用场景：已经购买了云盘实例的情况。

静态云盘使用方式：需手动创建PV及PVC。请参见[使用静态云盘卷](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/云盘存储卷/使用静态云盘卷.md)。

## 使用限制

-   云盘为阿里云存储团队提供的非共享存储，只能同时被一个Pod挂载。
-   集群中只有与云盘在同一个可用区（Zone）的节点才可以挂载云盘。

## 步骤一：创建PV

1.  使用以下内容，创建pv-static.yaml。

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: csi-pv
      labels:
        alicloud-pvname: static-disk-pv
    spec:
      capacity:
        storage: 25Gi
      accessModes:
        - ReadWriteOnce
      persistentVolumeReclaimPolicy: Retain
      csi:
        driver: diskplugin.csi.alibabacloud.com
        volumeHandle: "<your-disk-id>"
      nodeAffinity:
        required:
          nodeSelectorTerms:
          - matchExpressions:
            - key: topology.diskplugin.csi.alibabacloud.com/zone
              operator: In
              values:
              - "<your-node-zone-id>"
    ```

    |参数|说明|
    |--|--|
    |`name`|PV的名称。|
    |`labels`|设置PV的标签。|
    |`storage`|云盘的可使用量。|
    |`accessModes`|设置访问模式。|
    |`persistentVolumeReclaimPolicy`|PV的回收策略。|
    |`driver`|定义驱动类型。取值为diskplugin.csi.alibabacloud.com，表示使用阿里云云盘CSI插件。|
    |`volumeHandle`|定义云盘ID。|
    |`nodeAffinity`|定义PV/PVC所属的Zone信息。通过定义该参数，可以将PV/PVC所在的Pod调度到对应的Zone上。|

2.  执行以下命令，创建PV。

    ```
    kubectl create -f pv-static.yaml
    ```

    登录[容器服务管理控制台](https://cs.console.aliyun.com)。在控制台左侧导航栏中，单击**集群**。在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。在集群管理页左侧导航栏选择**存储** \> **存储卷**。可以在存储卷页面查看到创建的PV。


## 步骤二：创建PVC

1.  使用以下内容，创建pvc-static.yaml。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: csi-pvc
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 25Gi
      selector:
        matchLabels:
          alicloud-pvname: static-disk-pv
    ```

    |参数|说明|
    |--|--|
    |`name`|PVC的名称。|
    |`accessModes`|设置访问模式。|
    |`storage`|声明应用使用量，不能大于存储卷的总量。|
    |`matchLabels`|通过标签关联PV，与PV标签保存一致。|

2.  执行以下命令，创建PVC。

    ```
    kubectl create -f pvc-static.yaml
    ```

    在集群管理页左侧导航栏选择**存储** \> **存储声明**，在存储声明页面可以看到创建的PVC。


## 步骤三：创建应用

创建nginx-static应用，并在应用中挂载PVC，本文以Nginx应用为例。

1.  使用以下内容，创建static.yaml。

    创建一个名为的nginx-static应用，并在应用中挂载PVC。

    ```
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web
    spec:
      selector:
        matchLabels:
          app: nginx
      serviceName: "nginx"
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx
            ports:
            - containerPort: 80
              name: web
            volumeMounts:
            - name: pvc-disk
              mountPath: /data
          volumes:
            - name: pvc-disk
              persistentVolumeClaim:
                claimName: csi-pvc
    ```

    -   `mountPath`：云盘在容器中挂载的位置。
    -   `claimName`：PVC的名称，用于绑定PVC。
2.  执行以下命令，创建Nginx应用并挂载PVC。

    ```
    kubectl create -f static.yaml
    ```

    在集群管理页左侧导航栏选择**工作负载** \> **有状态**。您可以在有状态页面看到创建的应用。


## 验证静态云盘的持久化存储

1.  查看部署nginx-static应用的Pod和云盘文件。

    1.  执行以下命令，查看部署的nginx-static应用所在Pod的名称。

        ```
        kubectl get pod | grep static
        ```

        预期输出：

        ```
        nginx-xxxxxx   1/1     Running     0          32s
        ```

    2.  执行以下命令，查看/data路径下是否挂载了新的云盘。

        ```
        kubectl exec nginx-xxxxxx df | grep data
        ```

        预期输出：

        ```
        /dev/vdf        20511312    45080  20449848   1% /data
        ```

    3.  执行以下命令，查看/data路径下的文件。

        ```
        kubectl exec nginx-xxxxxx ls /data
        ```

        预期输出：

        ```
        lost+found
        ```

2.  执行以下命令，在/data路径下创建文件static。

    ```
    kubectl exec nginx-xxxxxx touch /data/static
    ```

3.  执行以下命令，查看/data路径下的文件。

    ```
     kubectl exec nginx-xxxxxx ls /data
    ```

    预期输出：

    ```
    static
    lost+found
    ```

4.  执行以下命令，删除名称为`nginx-xxxxxx`的Pod。

    ```
    kubectl delete pod nginx-xxxxxx
    ```

    预期输出：

    ```
    pod "nginx-xxxxxx" deleted
    ```

5.  验证删除Pod后，云盘里创建的文件是否还存在。

    1.  执行以下命令，查看重建的Pod名称。

        ```
        kubectl get pod
        ```

        预期输出：

        ```
        NAME                            READY   STATUS      RESTARTS   AGE
        nginx-xxxxxx                    1/1     Running     0          14s
        ```

    2.  执行以下命令，查看/data路径下的文件。

        ```
        kubectl exec nginx-xxxxxx ls /data
        ```

        预期输出：

        ```
        static
        lost+found
        ```

        static文件仍然存在，说明静态云盘的数据可持久化保存。


