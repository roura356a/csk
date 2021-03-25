# 使用NAS静态存储卷

NAS存储卷是一种可共享访问、弹性扩展、高可靠以及高性能的分布式文件系统。本文介绍如何使用阿里云NAS静态存储卷。

-   您已经创建一个静态NAS卷。请参见[管理文件系统]()。
-   您已经创建NAS挂载点。请参见[管理挂载点]()。

    NAS挂载点需要和集群节点在同一个VPC内。


## 通过kubectl命令行方式使用NAS静态存储卷

通过kubectl命令行的方式使用云盘静态存储卷前，请确保您可以使用kubectl命令链接集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

1.  创建静态PV。

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
          server: "2564f49129-ysu87.cn-shenzhen.nas.aliyuncs.com"
          path: "/csi"
      mountOptions:
      - nolock,tcp,noresvport
      - vers=3
    ```

    **说明：**

    -   `driver`：驱动类型。本例中取值为`nasplugin.csi.alibabacloud.com`，表示使用阿里云NAS CSI插件。
    -   `volumeHandle`：配置PV的名称。
    -   `server`：NAS挂载点。
    -   `path`：挂载子目录，极速NAS需要以/share开头。
    -   `vers`：挂载NAS数据卷的NFS协议版本号，推荐使用v3；极速类型NAS只支持v3。
2.  创建静态PVC。

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

3.  创建应用。

    以下为创建应用的YAML示例文件。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-nas
      labels:
        app: nginx
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
            image: nginx:1.7.9
            ports:
            - containerPort: 80
            volumeMounts:
              - name: pv-nas
                mountPath: "/data"
          volumes:
            - name: pv-nas
              persistentVolumeClaim:
                claimName: pvc-nas
    ```


## 通过控制台的方式使用NAS静态存储卷

**步骤一：创建PV**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**存储** \> **存储卷**。

5.  单击**存储卷**页签，在**存储卷**页面单击右上角的**创建**。

6.  在**创建存储卷**对话框中配置参数。

    |参数|说明|
    |--|--|
    |**存储卷类型**|支持云盘/NAS/OSS三种云存储类型。本文中选择为NAS。|
    |**名称**|创建的数据卷的名称。数据卷名在集群内必须唯一。本例为pv-nas。|
    |**存储驱动**|支持Flexvolume和CSI。本文中选择为CSI。|
    |**总量**|所创建存储卷的容量。注意NAS文件系统本身不限制使用量。此处不是NAS文件系统的使用限额，只是所创建存储卷的容量声明。|
    |**访问模式**|支持ReadWriteMany和ReadWriteOnce。默认为ReadWriteMany。|
    |**挂载点域名**|您可以通过**选择挂载点**或者自定义的方式定义集群在NAS文件系统中挂载点的挂载地址。|
    |**显示高级选项**|    -   **子目录**：NAS路径下的子目录，以/开头，设定后数据卷将挂载到指定的子目录。
        -   如果NAS根目录下没有此子目录，会默认创建后再挂载。
        -   您可以不填此项，默认挂载到NAS根目录。
        -   极速NAS需要以/share开头。
    -   **版本**：所创建存储卷的版本。 |
    |**标签**|为该存储卷添加标签。|

7.  参数配置完成后，单击**创建**。


**步骤二：创建PVC**

1.  在集群管理页左侧导航栏中，选择**存储** \> **存储声明**。

2.  在**存储声明**页签，单击右上角的**创建**。

3.  在弹出的创建存储声明页面中，填写界面参数。

    |参数|说明|
    |--|--|
    |**存储声明类型**|支持云盘、NAS、OSS三种云存储类型。 本文中选择NAS。|
    |**名称**|创建的存储声明名称在集群内必须唯一。|
    |**分配模式**|选择已有存储卷。**说明：** 若未创建存储卷，您可以设置**分配模式**为**创建存储卷**，配置创建存储卷参数，详情请参见[\#section\_kuh\_z2k\_6r2](#section_kuh_z2k_6r2)。 |
    |**已有存储卷**|单击**选择已有存储卷**，在目标存储卷右侧操作列单击**选择**，选择存储卷。|
    |**总量**|所创建存储卷的容量。**说明：** 所创建存储卷声明的容量不能超过待挂载的存储卷容量。 |

4.  单击**创建**。

    创建成功后可以在列表中看到创建的存储声明，并且已绑定相应的存储卷。


**步骤三：创建应用**

1.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

2.  在**无状态**页签中，单击**使用镜像创建**。

3.  配置创建应用的参数信息。

    以下主要为您介绍数据卷的配置。关于其他参数的描述，请参见[创建无状态工作负载Deployment](/intl.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建无状态工作负载Deployment.md)。

    ACK数据卷支持配置本地存储和云存储。

    -   **本地存储**：支持主机目录（HostPath）、配置项（ConfigMap）、保密字典（Secret）和临时目录，将对应的挂载源挂载到容器路径中。更多信息参见[volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE)。
    -   **云存储**：支持云存储类型。
    本例中配置了一个NAS类型的数据卷，将该NAS存储卷挂载到容器中/tmp路径下。

    ![数据卷](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6785659951/p59980.jpg)

4.  所有的信息都配置完成后，单击**创建**。

    创建成功后，您就可以正常使用数据卷。


