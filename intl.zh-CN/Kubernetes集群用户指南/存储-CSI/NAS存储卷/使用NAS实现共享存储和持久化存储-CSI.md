---
keyword: [NAS, 共享存储, 持久化存储]
---

# 使用NAS实现共享存储和持久化存储-CSI

NAS提供了共享存储和持久化存储的服务，满足您需要多个Pod共享数据的应用场景。本文为您介绍如何使用NAS实现共享存储和持久化存储。

-   [创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   您已在文件存储控制台创建一个文件系统，请参见[Linux系统挂载NFS文件系统]()。创建的文件系统需要与您的Kubernetes集群在同一可用区。
-   您已在创建好的文件系统中添加容器服务Kubernetes集群的挂载点，请参见[Windows系统挂载SMB文件系统]()。文件系统挂载时的VPC网络要与您Kubernetes集群所在的VPC网络保持一致。

NAS支持同时被多个Pod挂载，此时多个Pod可能同时修改相同数据，需要应用自行实现数据的同步。

**说明：** NAS存储的/目录不支持修改权限、属主和属组。

## 使用场景

-   对磁盘I/O要求较高的应用。
-   读写性能相对于对象存储OSS高。
-   可实现跨主机文件共享，例如可作为文件服务器。

## 使用NAS静态存储卷

**步骤一：创建PV**

1.  使用以下内容，创建pv-nas.yaml文件。

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: pv-nas
      labels:
        alicloud-pvname: pv-nas
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteMany
      csi:
        driver: nasplugin.csi.alibabacloud.com
        volumeHandle: pv-nas
        volumeAttributes:
          server: "2564f49129-ysu87.cn-shenzhen.nas.aliyuncs.com"
          path: "/csi"
      mountOptions:
      - nolock,tcp,noresvport
      - vers=3
    ```

    |参数|说明|
    |--|--|
    |`name`|PV的名称。|
    |`labels`|设置PV的标签。|
    |`storage`|NAS的可使用量。|
    |`accessModes`|配置访问模式。|
    |`driver`|驱动类型。本例中取值为`nasplugin.csi.alibabacloud.com`，表示使用阿里云NAS CSI插件。|
    |`volumeHandle`|配置PV的名称。|
    |`server`|NAS挂载点。|
    |`path`|挂载子目录，极速NAS需要以/share开头。|
    |`vers`|挂载NAS数据卷的NFS协议版本号，推荐使用v3，极速类型NAS只支持v3。|

2.  执行以下命令，创建PV。

    ```
    kubectl create -f pv-nas.yaml
    ```


**步骤二：创建PVC**

创建NAS存储声明PVC，使用selector筛选PV，精确配置PVC和PV的绑定关系。

1.  使用以下内容，创建pvc-nas.yaml。

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: pvc-nas
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 5Gi
      selector:
        matchLabels:
          alicloud-pvname: pv-nas
    ```

    |参数|说明|
    |--|--|
    |name|PVC的名称。|
    |accessModes|配置访问模式。|
    |storage|声明应用使用量，不能大于存储卷的总量。|
    |mathLabels|输入PV的标签，用于关联PV。|

2.  执行以下命令，创建PVC。

    ```
    kubectl create -f pvc-nas.yaml
    ```


**步骤三：创建应用**

1.  使用以下内容，创建nas.yaml文件。

    创建一个名为nas-static的应用，并挂载PVC。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nas-static
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
        spec:
          containers:
          - name: nginx
            image: nginx
            ports:
            - containerPort: 80
            volumeMounts:
              - name: pvc-nas
                mountPath: "/data"
          volumes:
            - name: pvc-nas
              persistentVolumeClaim:
                claimName: pvc-nas
    ```

    -   `mountPath`：NAS在容器中挂载的位置。
    -   `claimName`：PVC的名称，用于绑定PVC。
2.  执行以下命令，创建应用。

    ```
    kubectl create -f nas.yaml
    ```


## 使用NAS动态存储卷

**步骤一：创建StorageClass**

1.  使用以下内容，创建alicloud-nas-subpath.yaml。

    ```
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: alicloud-nas-subpath
    mountOptions:
    - nolock,tcp,noresvport
    - vers=3
    parameters:
      volumeAs: subpath
      server: "xxxxxxx.cn-hangzhou.nas.aliyuncs.com:/k8s/"
    provisioner: nasplugin.csi.alibabacloud.com
    reclaimPolicy: Retain
    ```

    |参数|描述|
    |--|--|
    |`name`|StorageClass的名称。|
    |`provisioner`|动态云盘配置为alicloud/disk，标识使用provisioner插件自动创建阿里云云盘。|
    |`reclaimPolicy`|NAS的回收策略。支持Delete和Retain，默认情况为Delete。**说明：** 如果配置为Delete，删除PVC时，NAS会一起删除，NAS上的数据不可恢复。 |

2.  执行以下命令，创建StorageClass。

    ```
    kubectl create -f alicloud-nas-subpath.yaml
    ```


**步骤二：创建PVC**

1.  使用以下内容，创建pvc.yaml。

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: nas-csi-pvc
    spec:
      accessModes:
        - ReadWriteMany
      storageClassName: alicloud-nas-subpath
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
    kubectl create -f pvc。yaml
    ```


**步骤三：创建应用**

创建nginx-1和nginx-2应用，共享NAS存储卷的同一个子目录。

**说明：** 如果您需要为不同的Pod挂载同一个NAS文件系统的不同子目录， 则需要分别创建pvc-1和nginx-1以及pvc-2和nginx-2。

1.  使用以下内容，创建nginx-1.yam。

    创建一个名为nginx-1的应用，并挂载PVC。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: deployment-nas-1
      labels:
        app: nginx-1
    spec:
      selector:
        matchLabels:
          app: nginx-1
      template:
        metadata:
          labels:
            app: nginx-1
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9
            ports:
            - containerPort: 80
            volumeMounts:
              - name: nas-pvc
                mountPath: "/data"
          volumes:
            - name: nas-pvc
              persistentVolumeClaim:
                claimName: nas-csi-pvc
    ```

    -   `mountPath`：NAS在容器中挂载的位置。
    -   `claimName`：PVC的名称，用于绑定PVC。本例中为nas-csi-pvc。
2.  使用以下内容，创建nginx-2.yaml。

    创建一个名为nginx-2的应用，并挂载PVC。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: deployment-nas-2
      labels:
        app: nginx-2
    spec:
      selector:
        matchLabels:
          app: nginx-2
      template:
        metadata:
          labels:
            app: nginx-2
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9
            ports:
            - containerPort: 80
            volumeMounts:
              - name: nas-pvc
                mountPath: "/data"
          volumes:
            - name: nas-pvc
              persistentVolumeClaim:
                claimName: nas-csi-pvc
    ```

    -   `mountPath`：NAS在容器中挂载的位置。本例为/date。
    -   `claimName`：输入与nginx-1应用相同的PVC名称，本例为nas-csi-pvc。
3.  执行以下命令，创建nginx-1和nginx-2应用。

    ```
    kubectl create -f nginx-1.yaml -f nginx-2.yaml
    ```

4.  执行以下命令，查看Pod信息。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                                READY   STATUS    RESTARTS   AGE
    deployment-nas-1-5b5cdb85f6-n****   1/1     Running   0          32s
    deployment-nas-2-c5bb4746c-4****    1/1     Running   0          32s
    ```

    **说明：** NAS存储卷的`xxxxxxx.cn-hangzhou.nas.aliyuncs.com:/share/nas-79438493-f3e0-11e9-bbe5-00163e09c2be`会同时挂载到`deployment-nas-1-5b5cdb85f6-nhklx`和`deployment-nas-2-c5bb4746c-4jw5l/`Pod的/data目录下。其中：

    -   `/share`：StorageClass中指定的subpath。
    -   `nas-79438493-f3e0-11e9-bbe5-00163e09c2be`：PV的名称。

## 验证NAS的持久化存储

使用NAS动态存储卷和NAS静态存储卷都支持NAS的持久化存储功能，以下以使用NAS动态存储卷为例。

1.  查看部署应用的Pod和NAS文件。

    1.  执行以下命令，查看部署的应用所在Pod的名称。

        ```
        kubectl get pod 
        ```

        预期输出：

        ```
        NAME                                READY   STATUS    RESTARTS   AGE
        deployment-nas-1-5b5cdb85f6-n****   1/1     Running   0          32s
        deployment-nas-2-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  执行以下命令，查看任意一个Pod/data路径下的文件，本文以名为deployment-nas-1-5b5cdb85f6-n\*\*\*\*的Pod为例。

        ```
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** ls /data
        ```

        无返回结果，说明/data路径下无文件。

2.  执行以下命令，在名为deployment-nas-1-5b5cdb85f6-n\*\*\*\*的Pod/data路径下创建文件nas。

    ```
    kubectl exec deployment-nas-1-5b5cdb85f6-n**** touch /data/nas
    ```

3.  执行以下命令，查看名为deployment-nas-1-5b5cdb85f6-n\*\*\*\*的Pod/data路径下的文件。

    ```
    kubectl exec deployment-nas-1-5b5cdb85f6-n**** ls /data
    ```

    预期输出：

    ```
    nas
    ```

4.  执行以下命令，删除Pod。

    ```
    kubectl delete pod deployment-nas-1-5b5cdb85f6-n****
    ```

5.  同时在另一个窗口中，执行以下命令，查看Pod删除及Kubernetes重建Pod的过程。

    ```
    kubectl get pod -w -l app=nginx
    ```

6.  验证删除Pod后，NAS里创建的文件是否还存在。

    1.  执行以下命令，查看Kubernetes重建的Pod名称。

        ```
        kubectl get pod
        ```

        预期输出：

        ```
        NAME                                READY   STATUS    RESTARTS   AGE
        deployment-nas-1-5b5cdb85f6-n****   1/1     Running   0          32s
        deployment-nas-2-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  执行以下命令，查看名为deployment-nas-1-5b5cdb85f6-n\*\*\*\*的Pod/data路径下的文件。

        ```
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** ls /data
        ```

        预期输出：

        ```
        nas
        ```

        nas文件仍然存在，说明NAS的数据可持久化保存。


## 验证NAS的共享存储

使用NAS动态存储卷和NAS静态存储卷都支持NAS的共享存储功能，以下以使用NAS动态存储卷为例。

1.  查看部署的应用所在的Pod和NAS文件。

    1.  执行以下命令，查看应用所在Pod的名称。

        ```
        kubectl get pod 
        ```

        预期输出：

        ```
        NAME                                READY   STATUS    RESTARTS   AGE
        deployment-nas-1-5b5cdb85f6-n****   1/1     Running   0          32s
        deployment-nas-2-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  执行以下命令，查看2个Pod/data路径下的文件。

        ```
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** ls /data
        kubectl exec deployment-nas-2-c5bb4746c-4**** ls /data
        ```

2.  执行以下命令，在任意一个Pod的/date路径下创建文件nas。

    ```
     kubectl exec deployment-nas-1-5b5cdb85f6-n**** touch /data/nas
    ```

3.  执行以下命令，查看2个Pod/date路径下的文件。

    1.  执行以下命令，查看名为deployment-nas-1-5b5cdb85f6-n\*\*\*\*的Pod/date路径下的文件。

        ```
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** ls /data
        ```

        预期输出：

        ```
        nas
        ```

    2.  执行以下命令，查看名为deployment-nas-2-c5bb4746c-4\*\*\*\*的Pod/date路径下的文件。

        ```
        kubectl exec deployment-nas-2-c5bb4746c-4**** ls /data
        ```

        预期输出：

        ```
        nas
        ```

        在任意一个Pod的/date下创建的文件，两个Pod下的/date路径下均存在此文件，说明两个Pod共享一个NAS。


