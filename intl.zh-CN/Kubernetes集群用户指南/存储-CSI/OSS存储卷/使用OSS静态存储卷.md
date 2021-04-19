---
keyword: [OSS, 静态存储, K8s]
---

# 使用OSS静态存储卷

对象存储OSS（Object Storage Service）是阿里云提供的海量、安全、低成本、高持久的云存储服务。本文为您介绍如何通过命令行及控制台的方式使用OSS静态存储卷。

[创建存储空间](/intl.zh-CN/快速入门/控制台快速入门/创建存储空间.md)。

**说明：** 相同地域的OSS Bucket可以使用内网地址进行挂载。

## 通过kubectl命令行的方式使用OSS静态存储卷

通过kubectl命令行的方式使用OSS静态存储卷前，请确保您可以使用kubectl命令链接集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

**步骤一：创建静态PV及PVC**

您有三种方式配置静态PV及PVC。

方式一：使用Secret创建静态卷PV及PVC。

1.  创建Secret。

    以下为通过Secret来配置用户的AccessKey信息的YAML示例文件。

    ```
    apiVersion: v1
    kind: Secret
    metadata:
      name: oss-secret
      namespace: default
    stringData:
      akId: ***
      akSecret: ***
    ```

    **说明：** 创建Secret选择的Namespace需要和应用所在Namespaces一致。

    本示例中，`akId`和`akSecret`需要替换成用户的AccessKey ID和AccessKey Secret。

2.  创建静态卷PV及PVC。

    以下为创建静态卷PV及PVC的YAML示例文件。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: oss-pvc
    spec:
      accessModes:
      - ReadWriteMany
      resources:
        requests:
          storage: 5Gi
    ---
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: oss-csi-pv
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteMany
      persistentVolumeReclaimPolicy: Retain
      csi:
        driver: ossplugin.csi.alibabacloud.com
        volumeHandle: oss-csi-pv // 需要和PV名字一致。
        nodePublishSecretRef:
          name: oss-secret
          namespace: default
        volumeAttributes:
          bucket: "oss"
          url: "oss-cn-hangzhou.aliyuncs.com"
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"
          path: "/"
    ```

    |参数|描述|
    |--|--|
    |`nodePublishSecretRef`|定义挂载PV时通过Secret对象来获取AccessKey信息。|
    |`volumeHandle`|配置PV的名称。|
    |`bucket`|需要挂载的OSS Bucket。|
    |`url`|挂载OSS的接入域名，挂载节点和Bucket相同地域时，可使用内网地址。|
    |`otherOpts`|挂载OSS时支持输入定制化参数，格式为：`-o *** -o ***`。|
    |`path`|表示挂载时相对Bucket根文件的目录结构，默认为/（v1.14.8.32-c77e277b-aliyun及之后版本支持）。|


方式二：配置PV及PVC的AccessKey。

[方式一：使用Secret创建静态卷PV及PVC](#p_dum_5ej_hg0)是通过Secret为CSI插件提供AccessKey信息，您也可以直接在PV中配置AccessKey信息。示例YAML文件如下。

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: oss-csi-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: ossplugin.csi.alibabacloud.com
    volumeHandle: oss-csi-pv // 需要和PV名字一致。
    volumeAttributes:
      bucket: "oss"
      url: "oss-cn-hangzhou.aliyuncs.com"
      otherOpts: "-o max_stat_cache_size=0 -o allow_other"
      akId: "***"
      akSecret: "***"
```

方式三：配置PV及PVC的STS权限。

除了[方式一：使用Secret创建静态卷PV及PVC](#p_dum_5ej_hg0)和[方式二：配置PV及PVC的AccessKey](#p_mfp_yr3_ovq)中使用Secret和使用AK两种方式进行权限认证外，您还可以在PV中配置STS进行权限认证，示例YAML文件如下。

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: oss-csi-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: ossplugin.csi.alibabacloud.com
    volumeHandle: oss-csi-pv // 需要和PV名字一致。
    volumeAttributes:
      bucket: "oss"
      url: "oss-cn-hangzhou.aliyuncs.com"
      otherOpts: "-o max_stat_cache_size=0 -o allow_other"
      authType: "sts"
```

**步骤二：创建应用。**

创建挂载OSS静态存储卷的应用YAML示例如下。

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
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
          - name: oss-pvc
            mountPath: "/data"
      volumes:
        - name: oss-pvc
          persistentVolumeClaim:
            claimName: oss-pvc
```

## 通过控制台的方式使用OSS静态存储卷

**步骤一：创建PV**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**存储** \> **存储卷**。

5.  在**存储卷**页面单击右上角的**创建**。

6.  在**创建存储卷**对话框中配置参数。

    |参数|描述|
    |--|--|
    |**存储卷类型**|支持云盘/NAS/OSS三种云存储类型。本文中选择为OSS。|
    |**名称**|创建的数据卷的名称。数据卷名在集群内必须唯一。本例为pv-oss。|
    |**存储驱动**|支持Flexvolume和CSI。本文中选择为 CSI。|
    |**总量**|所创建存储卷的容量。|
    |**访问模式**|默认为ReadWriteMany。|
    |**AccessKey ID**|访问OSS所需的AccessKey ID。|
    |**AccessKey Secret**|访问OSS所需的AccessKey Secret。|
    |**可选参数**|您可以为OSS存储卷输入定制化参数，格式为`-o *** -o ***`。|
    |**Bucket ID**|您要使用的OSS bucket的名称。单击**选择 Bucket**，在弹出的对话框中选择所需的bucket并单击**选择**。|
    |**访问域名**|您可以参考以下情况选择需要的访问域名。    -   如果Bucket和ECS实例位于不同地域（Region），请选择**外网域名**。
    -   如果Bucket和ECS实例位于相同地域，需要根据集群网络类型进行选择：
        -   若是VPC网络，请选择**VPC域名**。
        -   若是经典网络，请选择**内网域名**。 |
    |**标签**|为该存储卷添加标签。|

7.  参数配置完成后，单击**创建**。


**步骤二：创建PVC**

1.  在集群管理页左侧导航栏中，选择**存储** \> **存储声明**。

2.  在**存储声明**页面，单击右上角的**创建**。

3.  在创建存储声明页面中，填写界面参数。

    |参数|描述|
    |--|--|
    |**存储声明类型**|支持云盘、NAS、OSS三种云存储类型。本文中选择OSS。|
    |**名称**|创建的数据卷的名称，数据卷名在集群内必须唯一。|
    |**分配模式**|选择已有存储卷。**说明：** 若未创建存储卷，您可以设置**分配模式**为**创建存储卷**，配置创建存储卷参数，详情请参见[步骤一：创建PV](#p_fku_upj_qhs)。 |
    |**已有存储卷**|单击**选择已有存储类**，在目标存储卷右侧操作列单击**选择**，选择存储卷。|
    |**总量**|所创建存储卷的容量。**说明：** 所创建的存储卷容量不能超过磁盘容量。 |

4.  单击**创建**。

    创建成功后可以在列表中看到csi-oss-pvc，并且已绑定相应的存储卷。


**步骤三：创建应用**

1.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

2.  在**无状态**页面中，单击**使用镜像创建**。

3.  配置创建应用的参数信息。

    以下主要为您介绍数据卷的配置。关于其他参数的描述，请参见[创建无状态工作负载Deployment](/intl.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建无状态工作负载Deployment.md)。

    ACK数据卷支持配置本地存储和云存储。

    -   **本地存储**：支持主机目录（HostPath）、配置项（ConfigMap）、保密字典（Secret）和临时目录，将对应的挂载源挂载到容器路径中。更多信息参见[volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE)。
    -   **云存储**：支持云存储类型。
    本例中配置了一个OSS类型的数据卷，将该OSS存储卷挂载到容器中/tmp 路径下。

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0885659951/p69158.png)

4.  所有的信息都配置完成后，单击**创建**。

    创建成功后，您就可以正常使用数据卷。


