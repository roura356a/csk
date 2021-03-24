---
keyword: [动态云盘, 持久化存储, CSI]
---

# 使用云盘动态存储卷实现持久化存储-CSI

当容器发生宕机故障时，有状态服务容器存储的业务数据存在着丢失和不可靠等风险。使用持久化存储可以解决该问题。本文介绍如何使用云盘动态存储卷实现持久化存储。

[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)

**说明：** 创建集群时需要安装Provisioner插件，默认安装Provisioner插件。该插件可以根据StorageClass自动创建云盘。

动态云盘的使用场景：没有购买云盘，在应用部署时自动购买云盘的情况。

动态云盘的使用方式：

1.  手动创建PVC，在PVC中声明StorageClass。
2.  部署应用时通过StorageClass自动创建PV。

## 步骤一：创建StorageClass

阿里云容器服务Kubernetes在系统初始化的时候会默认创建4个StorageClass，且使用参数的默认情况。同时这4个StorageClass仅适用于单可用区集群，若是多可用区集群，则需要您创建StorageClass。这4个StorageClass分别为：

-   alicloud-disk-common：自动创建普通云盘。
-   alicloud-disk-efficiency：自动创建高效云盘。
-   alicloud-disk-ssd：自动创建SSD云盘。
-   alicloud-disk-available：提供高可用选项，先尝试自动创建SSD云盘；如果相应可用区的SSD云盘资源售尽，再尝试自动创建高效云盘。

1.  使用以下内容，创建storageclass.yaml文件。

    ```
    kind: StorageClass
    apiVersion: storage.k8s.io/v1beta1
    metadata:
      name: alicloud-disk-ssd-hangzhou-b
    provisioner: diskplugin.csi.alibabacloud.com
    reclaimPolicy: Retain
    parameters:
      type: cloud_ssd
      regionId: cn-hangzhou
      zoneId: cn-hangzhou-b
      fstype: "ext4"
      readonly: "false"
    ```

    |参数|描述|
    |--|--|
    |`name`|StorageClass的名称。|
    |`provisioner`|动态云盘配置为alicloud/disk，标识使用provisioner插件自动创建阿里云云盘。|
    |`reclaimPolicy`|云盘的回收策略。支持Delete和Retain，默认情况为Delete。**说明：** 如果配置为Delete，删除PVC时，云盘会一起删除，云盘上的数据不可恢复。 |
    |`type`|自动创建云盘的类型，支持cloud、cloud\_efficiency、cloud\_ssd、available。|
    |`regionId`|（可选）自动创建云盘所在的地域，与集群的地域相同。|
    |`zoneId`|（可选）自动创建云盘所在的区域。    -   单可用区集群，与集群所在区域相同。
    -   多可用区集群，zoneid可同时配置多个，例如：

        ```
zoneid: cn-hangzhou-a,cn-hangzhou-b,cn-hangzhou-c
        ``` |
    |`fstype`|（可选）自动创建云盘所使用的文件系统，默认情况为ext4。|
    |`readonly`|（可选）挂载自动创建云盘的权限是否为可读，支持true：云盘具有只读权限，false：云盘具有可读可写权限，默认情况为false。|
    |`encrypted`|（可选）自动创建的云盘是否加密，支持true：加密，false：不加密，默认情况为false。|

2.  执行以下命令，创建StorageClass。

    ```
    kubectl create -f storageclass.yaml
    ```

3.  查看创建的StorageClass。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群管理页左侧导航栏选择**存储** \> **存储类**。可以在存储类页面查看到创建的StorageClass。


## 步骤二：创建PVC

1.  使用以下内容，创建pvc-ssd.yaml文件。

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: disk-ssd
    spec:
      accessModes:
        - ReadWriteOnce
      storageClassName: alicloud-disk-ssd-hangzhou-b
      resources:
        requests:
          storage: 20Gi
    ```

    |参数|说明|
    |--|--|
    |`name`|PVC的名称。|
    |`accessModes`|配置访问模式。|
    |`storageClassName`|StorageClass的名称，用于绑定StorageClass。|
    |`storage`|声明应用存储使用量。|

2.  执行以下命令，创建PVC。

    ```
    kubectl create -f pvc-ssd.yaml
    ```

3.  查看创建的PVC。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群管理页左侧导航栏选择**存储** \> **存储声明**。可以在存储声明页面可以看到创建的PVC。


## 步骤三：创建应用

1.  创建pvc-dynamic.yaml文件。

    创建一个名为nginx-dynamic的应用，并挂载PVC。

    ```
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: nginx-dynamic
    spec:
      selector:
        matchLabels:
          app: nginx
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
                claimName: disk-ssd
    ```

    -   `mountPath`：云盘挂载的位置。
    -   `claimName`：PVC的名称，用于绑定PVC。
2.  执行以下命令，创建应用并挂载PVC。

    ```
    kubectl create -f nginx-dynamic.yaml
    ```

    在集群管理页左侧导航栏选择**工作负载** \> **有状态**。您可以在有状态页面看到创建的应用。


## 验证动态云盘的持久化存储

1.  查看nginx-dynamic应用所在的Pod和云盘文件。

    1.  执行以下命令，查看nginx-dynamic应用所在Pod的名称。

        ```
        kubectl get pod | grep dynamic
        ```

        预期输出：

        ```
        nginx-dynamic-xxx   1/1     Running     0          3m
        ```

    2.  执行以下命令，查看/data路径下是否挂载了新的云盘。

        ```
        kubectl exec nginx-dynamic-xxx df | grep data
        ```

        预期输出：

        ```
        /dev/vdh        20511312    45080  20449848   1% /data
        ```

    3.  执行以下命令，查看/data路径下的文件。

        ```
        kubectl exec nginx-dynamic-xxx ls /data
        ```

        预期输出：

        ```
        lost+found
        ```

2.  在云盘里创建文件。

    1.  执行以下命令，在/data路径下创建文件dynamic。

        ```
        kubectl exec nginx-dynamic-xxx touch /data/dynamic
        ```

    2.  执行以下命令，查看/data路径下的文件。

        ```
        kubectl exec nginx-dynamic-xxx ls /data
        ```

        预期输出：

        ```
        dynamic
        lost+found
        ```

3.  执行以下命令，删除名为`nginx-dynamic-xxx`的Pod。

    ```
    kubectl delete pod nginx-dynamic-xxx
    ```

    预期输出：

    ```
    pod "nginx-dynamic-xxx" deleted
    ```

4.  验证删除Pod后，云盘里创建的文件是否还存在。

    1.  执行以下命令，查看重建的Pod名称。

        ```
        kubectl get pod 
        ```

        预期输出：

        ```
        NAME                               READY   STATUS      RESTARTS   AGE
        nginx-dynamic-xxx                   1/1     Running     0          2m
        ```

    2.  执行以下命令，查看/data路径下的文件。

        ```
        kubectl exec nginx-dynamic-xxx ls /data
        ```

        预期输出：

        ```
        dynamic
        lost+found
        ```

        dynamic文件仍然存在，说明动态云盘的数据可持久保存。


