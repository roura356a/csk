---
keyword: [NAS静态存储卷, 持久化存储, 共享存储]
---

# 使用NAS静态存储卷

NAS存储卷是一种可共享访问、弹性扩展、高可靠以及高性能的分布式文件系统。本文介绍如何使用阿里云NAS静态存储卷，及如何实现持久化存储与共享存储。

-   已创建Kubernetes集群。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已创建静态NAS卷。具体操作，请参见[创建文件系统]()。

    若需要加密NAS存储卷中的数据，创建NAS文件系统时请配置加密类型。

-   已创建NAS挂载点。具体操作，请参见[管理挂载点]()。

    NAS挂载点需要和集群节点在同一个VPC内。

-   [步骤二：选择集群凭证类型](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

## 使用场景

-   对磁盘I/O要求较高的应用。
-   读写性能相对于对象存储OSS高。
-   可实现跨主机文件共享，例如可作为文件服务器。

## 注意事项

-   在使用极速NAS文件系统时，配置数据卷的`path`需要以/share为父目录。例如，Pod挂载的NAS文件系统子目录可配置为/share/path1。
-   NAS支持同时被多个Pod挂载，此时多个Pod可能同时修改相同数据，需要应用自行实现数据的同步。

**说明：** NAS存储的/目录不支持修改权限、属主和属组。

## 通过控制台的方式使用NAS静态存储卷

**步骤一：创建PV**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**存储** \> **存储卷**。

5.  在**存储卷**页面单击右上角的**创建**。

6.  在**创建存储卷**对话框中配置参数。

    |参数|说明|
    |--|--|
    |**存储卷类型**|支持云盘、NAS、OSS三种云存储类型。本文中选择为NAS。|
    |**名称**|创建的数据卷的名称。数据卷名在集群内必须唯一。本例为pv-nas。|
    |**存储驱动**|支持Flexvolume和CSI。本文中选择为CSI。|
    |**总量**|所创建存储卷的容量。注意NAS文件系统本身不限制使用量。此处不是NAS文件系统的使用限额，只是所创建存储卷的容量声明。|
    |**访问模式**|支持ReadWriteMany和ReadWriteOnce。默认为ReadWriteMany。|
    |**挂载点域名**|您可以通过**选择挂载点**或者**自定义**的方式定义集群在NAS文件系统中挂载点的挂载地址。|
    |**显示高级选项**|    -   **子目录**：NAS路径下的子目录，以/为根目录，设定后数据卷将挂载到指定的子目录。
        -   如果NAS根目录下没有此子目录，会默认创建后再挂载。
        -   您可以不填此项，默认挂载到NAS根目录。
        -   极速NAS需要以/share为父目录。
    -   **版本**：所创建存储卷的版本。 |
    |**标签**|为该存储卷添加标签。|

7.  参数配置完成后，单击**创建**。


**步骤二：创建PVC**

1.  在集群管理页左侧导航栏中，选择**存储** \> **存储声明**。

2.  在**存储声明**页面，单击右上角的**创建**。

3.  在弹出的创建存储声明页面中，填写界面参数。

    |参数|说明|
    |--|--|
    |**存储声明类型**|支持云盘、NAS、OSS三种云存储类型。 本文中选择NAS。|
    |**名称**|创建的存储声明名称在集群内必须唯一。|
    |**分配模式**|选择已有存储卷。**说明：** 若未创建存储卷，您可以设置**分配模式**为**创建存储卷**，配置创建存储卷参数。更多信息，请参见[创建PV](#p_93m_jyx_d0a)。 |
    |**已有存储卷**|单击**选择已有存储卷**，在目标存储卷右侧操作列单击**选择**，选择存储卷。|
    |**总量**|所创建存储卷的容量。**说明：** 所创建存储卷声明的容量不能超过待挂载的存储卷容量。 |

4.  单击**创建**。

    创建成功后可以在列表中看到创建的存储声明，并且已绑定相应的存储卷。


**步骤三：创建应用**

1.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

2.  在**无状态**页面中，单击**使用镜像创建**。

3.  配置创建应用的参数信息。

    以下主要为您介绍数据卷的配置。关于其他参数的描述，请参见[创建无状态工作负载Deployment](/cn.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建无状态工作负载Deployment.md)。

    ACK数据卷支持配置本地存储和云存储。

    -   **本地存储**：支持主机目录（HostPath）、配置项（ConfigMap）、保密字典（Secret）和临时目录，将对应的挂载源挂载到容器路径中。更多信息，请参见[Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE)。
    -   **云存储**：支持云存储类型。
    本例中配置了一个NAS类型的数据卷，将该NAS存储卷挂载到容器中/tmp路径下。

    ![数据卷](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6785659951/p59980.jpg)

4.  所有的信息都配置完成后，单击**创建**。

    创建成功后，您就可以正常使用数据卷。


## 通过kubectl命令行方式使用NAS静态存储卷

1.  执行以下命令创建静态PV。

    ```
    kubectl create -f pv-nas.yaml
    ```

    以下为创建静态卷PV的YAML示例文件。

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
          server: "2564f4****-ysu87.cn-shenzhen.nas.aliyuncs.com"
          path: "/csi"
      mountOptions:
      - nolock,tcp,noresvport
      - vers=3
    ```

    |参数|说明|
    |--|--|
    |name|PV的名称。|
    |labels|设置PV的标签。|
    |storage|NAS的可使用量。|
    |accessModes|配置访问模式。|
    |driver|驱动类型。本例中取值为`nasplugin.csi.alibabacloud.com`，表示使用阿里云NAS CSI插件。|
    |volumeHandle|配置PV的唯一标识符。若需要同时使用多个PV，则各个PV中该值必须不一致。|
    |server|NAS挂载点。|
    |path|挂载子目录，极速NAS需要以/share为父目录。|
    |vers|挂载NAS数据卷的NFS协议版本号，推荐使用v3，极速类型NAS只支持v3。|

2.  执行以下命令创建静态PVC。

    创建NAS存储声明PVC，使用selector筛选PV，精确配置PVC和PV的绑定关系。

    ```
    kubectl create -f pvc-nas.yaml
    ```

    以下为创建静态卷PVC的YAML示例文件。

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

3.  执行以下命令创建名为**nas-static**的应用，并挂载PVC。

    ```
    kubectl create -f nas.yaml
    ```

    以下为创建**nas-static**应用的nas.yaml示例文件。

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

    |参数|说明|
    |--|--|
    |mountPath|NAS在容器中挂载的位置。|
    |claimName|PVC的名称，用于绑定PVC。|

4.  执行以下命令，查看Pod信息。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    nas-static-5b5cdb85f6-n****   1/1     Running   0          32s
    nas-static-c5bb4746c-4****    1/1     Running   0          32s
    ```


## 验证NAS的持久化存储

1.  查看部署应用和NAS文件。

    1.  执行以下命令，查看部署的应用名称。

        ```
        kubectl get pod 
        ```

        预期输出：

        ```
        NAME                          READY   STATUS    RESTARTS   AGE
        nas-static-5b5cdb85f6-n****   1/1     Running   0          32s
        nas-static-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  执行以下命令，查看任意一个应用的/data路径下的文件，本文以名为`nas-static-5b5cdb85f6-n****`的Pod为例。

        ```
        kubectl exec nas-static-5b5cdb85f6-n**** ls /data
        ```

        无返回结果，说明/data路径下无文件。

2.  执行以下命令，在名为`nas-static-5b5cdb85f6-n****`Pod的/data路径下创建文件nas。

    ```
    kubectl exec nas-static-5b5cdb85f6-n**** touch /data/nas
    ```

3.  执行以下命令，查看名为`nas-static-5b5cdb85f6-n****`Pod的/data路径下的文件。

    ```
    kubectl exec nas-static-5b5cdb85f6-n**** ls /data
    ```

    预期输出：

    ```
    nas
    ```

4.  执行以下命令，删除Pod。

    ```
    kubectl delete pod nas-static-5b5cdb85f6-n****
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
        nas-static-5b5cdb85f6-n****   1/1     Running   0          32s
        nas-static-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  执行以下命令，查看名为`nas-static-5b5cdb85f6-n****`的Pod /data路径下的文件。

        ```
        kubectl exec nas-static-5b5cdb85f6-n**** ls /data
        ```

        预期输出：

        ```
        nas
        ```

        nas文件仍然存在，说明NAS的数据可持久化保存。


## 验证NAS的共享存储

1.  查看部署的应用所在的Pod和NAS文件。

    1.  执行以下命令，查看应用所在Pod的名称。

        ```
        kubectl get pod 
        ```

        预期输出：

        ```
        NAME                          READY   STATUS    RESTARTS   AGE
        nas-static-5b5cdb85f6-n****   1/1     Running   0          32s
        nas-static-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  执行以下命令，查看2个Pod /data路径下的文件。

        ```
        kubectl exec nas-static-5b5cdb85f6-n**** ls /data
        kubectl exec nas-static-c5bb4746c-4**** ls /data
        ```

2.  执行以下命令，在任意一个Pod的/data路径下创建文件nas。

    ```
     kubectl exec nas-static-5b5cdb85f6-n**** touch /data/nas
    ```

3.  执行以下命令，查看2个Pod /data路径下的文件。

    1.  执行以下命令，查看名为`nas-static-5b5cdb85f6-n****`的Pod /data路径下的文件。

        ```
        kubectl exec nas-static-5b5cdb85f6-n**** ls /data
        ```

        预期输出：

        ```
        nas
        ```

    2.  执行以下命令，查看名为`nas-static-c5bb4746c-4****`的Pod /data路径下的文件。

        ```
        kubectl exec nas-static-c5bb4746c-4**** ls /data
        ```

        预期输出：

        ```
        nas
        ```

        如果在任意一个Pod的/data下创建的文件，两个Pod下的/data路径下均存在此文件，则说明两个Pod共享一个NAS。


