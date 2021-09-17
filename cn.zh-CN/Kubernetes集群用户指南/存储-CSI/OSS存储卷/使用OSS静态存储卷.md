---
keyword: [OSS, 静态存储, K8s]
---

# 使用OSS静态存储卷

对象存储OSS（Object Storage Service）是阿里云提供的海量、安全、低成本、高持久的云存储服务。本文为您介绍如何通过命令行及控制台的方式使用OSS静态存储卷。

-   已创建ACK集群。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已创建Bucket。具体操作，请参见[创建存储空间](/cn.zh-CN/快速入门/控制台快速入门/创建存储空间.md)。

    **说明：** 若Bucket和ECS实例位于相同地域，请选择私网域名。

-   已通过kubectl连接Kubernetes集群。具体操作，请参见[步骤二：选择集群凭证类型](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

阿里云对象存储服务（OSS）提供海量、安全、低成本、高可靠的云存储服务。OSS支持同时被多个Pod挂载。以下为OSS的使用场景：

-   对磁盘I/O要求低。
-   配置文件、图片、小视频等共享业务。

## 注意事项

-   OSS不支持动态创建PV。
-   容器服务Kubernetes集群升级会重启Kubelet，OSSFS驱动跟随一起重启，导致OSS目录不可用。这时使用OSS的Pod需要重建，可在YAML文件中增加健康检查的配置，在容器内OSS目录不可用时自动重启Pod，重新挂载OSS。

    **说明：** 如果您使用的是v1.18.8.45及以上版本的csi-plugin和csi-provisioner插件，则不会出现上述问题。

-   若您在应用模板中配置了securityContext.fsgroup参数，kubelet在存储卷挂载完成后会执行`chmod`或`chown`操作，导致挂载时间延长。

    **说明：** 若已配置securityContext.fsgroup参数，且需要减少挂载时间。具体操作，请参见[OSS存储卷挂载时间延长]()。


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
    |**访问证书**|选择访问OSS所需的保密字典。    -   **选择已有保密字典**：选择命名空间及保密字典。
    -   **新建保密字典**：配置命名空间、名称、AccessKey ID及AccessKey Secret。 |
    |**可选参数**|您可以为OSS存储卷输入定制化参数，格式为`-o *** -o ***`。|
    |**Bucket ID**|您要使用的OSS bucket的名称。单击**选择 Bucket**，在弹出的对话框中选择所需的bucket并单击**选择**。|
    |**访问域名**|您可以参考以下情况选择需要的访问域名。    -   如果Bucket和ECS实例位于不同地域（Region），请选择**公网域名**。
    -   如果Bucket和ECS实例位于相同地域，请选择**私网域名**。 |
    |**标签**|为该存储卷添加标签。|

7.  参数配置完成后，单击**创建**。


**步骤二：创建PVC**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**存储** \> **存储声明**。

5.  在**存储声明**页面，单击右上角的**创建**。

6.  在创建存储声明页面中，填写界面参数。

    |参数|描述|
    |--|--|
    |**存储声明类型**|支持云盘、NAS、OSS三种云存储类型。本文中选择OSS。|
    |**名称**|创建的数据卷的名称，数据卷名在集群内必须唯一。|
    |**分配模式**|选择已有存储卷。**说明：** 若未创建存储卷，您可以设置**分配模式**为**创建存储卷**，配置创建存储卷参数。具体操作，请参见[步骤一：创建PV](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/OSS存储卷/使用OSS静态存储卷.md)。 |
    |**已有存储卷**|单击**选择已有存储类**，在目标存储卷右侧操作列单击**选择**，选择存储卷。|
    |**总量**|所创建存储卷的容量。**说明：** 所创建的存储卷容量不能超过存储空间容量。 |

7.  单击**创建**。

    创建成功后可以在列表中看到**csi-oss-pvc**，并且已绑定相应的存储卷。


**步骤三：创建应用**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在**无状态**页面中，单击**使用镜像创建**。

6.  配置创建应用的参数信息。

    以下主要为您介绍数据卷的配置。关于其他参数的描述，请参见[创建无状态工作负载Deployment](/cn.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建无状态工作负载Deployment.md)。

    ACK数据卷支持配置本地存储和云存储。

    -   **本地存储**：支持主机目录（HostPath）、配置项（ConfigMap）、保密字典（Secret）和临时目录，将对应的挂载源挂载到容器路径中。更多信息参见[volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE)。
    -   **云存储**：支持云存储类型。
    本例中配置了一个OSS类型的数据卷，将该OSS存储卷挂载到容器中/tmp 路径下。

    ![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0885659951/p69158.png)

7.  所有的信息都配置完成后，单击**创建**。

    创建成功后，您就可以正常使用数据卷。


## 通过kubectl命令行的方式使用OSS静态存储卷

**步骤一：创建静态PV及PVC**

您有三种方式配置静态PV及PVC。

-   方式一：使用Secret创建静态卷PV及PVC

    通过Secret为CSI插件提供AccessKey信息。

-   方式二：配置PV及PVC的AccessKey

    直接在PV中配置AccessKey信息。

-   方式三：配置PV及PVC的STS权限

    在PV中配置STS进行权限认证。


方式一：使用Secret创建静态卷PV及PVC

1.  创建Secret。

    以下为通过Secret来配置AccessKey信息的YAML示例文件。

    ```
    apiVersion: v1
    kind: Secret
    metadata:
      name: oss-secret
      namespace: default
    stringData:
      akId: <yourAccessKey ID>
      akSecret: <yourAccessKey Secret>
    ```

    **说明：** 创建Secret选择的Namespace需要和应用所在Namespaces一致。

    本示例中，`akId`和`akSecret`需要替换成用户的AccessKey ID和AccessKey Secret。

2.  执行以下命令创建静态卷PV。

    ```
    kubectl create -f pv-oss.yaml
    ```

    以下为创建静态卷PV的pv-oss.yaml示例文件。

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: pv-oss
      labels:
        alicloud-pvname: pv-oss
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteMany
      persistentVolumeReclaimPolicy: Retain
      csi:
        driver: ossplugin.csi.alibabacloud.com
        volumeHandle: pv-oss // 需要和PV名字一致。
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
    |name|PV的名称。|
    |labels|配置PV的标签。|
    |storage|OSS的可使用量。|
    |accessModes|配置访问模式。|
    |persistentVolumeReclaimPolicy|PV回收策略。|
    |driver|定义驱动类型。取值为ossplugin.csi.alibabacloud.com，表示使用OSS CSI插件。|
    |nodePublishSecretRef|定义挂载PV时通过Secret对象来获取AccessKey信息。|
    |volumeHandle|配置PV的名称。|
    |bucket|需要挂载的OSS Bucket。目前只支持挂载存储空间Bucket，不支持挂载Bucket下面的子目录或文件。|
    |url|挂载OSS的接入域名。    -   挂载节点和Bucket相同地域时，请使用私网地址。
    -   挂载节点和Bucket不同地域时，请使用公网地址。
    -   禁止使用VPC网络。 |
    |otherOpts|挂载OSS时支持输入定制化参数，格式为：`-o *** -o ***`。|
    |path|表示挂载时相对Bucket根文件的目录结构，默认为/（v1.14.8.32-c77e277b-aliyun及之后版本支持）。|

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在控制台左侧导航栏中，单击**集群**。
    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
    4.  在集群管理页左侧导航栏选择**存储** \> **存储卷**。可以在存储卷页面查看到创建的PV。
3.  执行以下命令创建静态卷PVC。

    ```
    kubectl create -f pvc-oss.yaml
    ```

    以下为创建静态卷PVC的pvc-oss.yaml示例文件。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: pvc-oss
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 5Gi
      selector:
        matchLabels:
          alicloud-pvname: pv-oss
    ```

    |参数|说明|
    |--|--|
    |`name`|PVC的名称。|
    |`accessModes`|配置访问模式。|
    |`storage`|声明应用使用量，不能大于存储卷的总量。|
    |`alicloud-pvname`|通过标签关联PV，与PV标签保持一致。|

    在集群管理页左侧导航栏选择**存储** \> **存储声明**，在存储声明页面可以看到创建的PVC。


方式二：配置PV及PVC的AccessKey

[方式一：使用Secret创建静态卷PV及PVC](#p_dum_5ej_hg0)是通过Secret为CSI插件提供AccessKey信息，您也可以执行以下命令直接在PV中配置AccessKey信息。

```
kubectl create -f pv-accesskey.yaml
```

以下为配置AccessKey信息的pv-accesskey.yaml示例文件。

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-oss
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: ossplugin.csi.alibabacloud.com
    volumeHandle: pv-oss // 需要和PV名字一致。
    volumeAttributes:
      bucket: "oss"
      url: "oss-cn-hangzhou.aliyuncs.com"
      otherOpts: "-o max_stat_cache_size=0 -o allow_other"
      akId: "***"
      akSecret: "***"
```

方式三：配置PV及PVC的STS权限

除了[方式一：使用Secret创建静态卷PV及PVC](#p_dum_5ej_hg0)和[方式二：配置PV及PVC的AccessKey](#p_mfp_yr3_ovq)中使用Secret和使用AccessKey两种方式进行权限认证外。

您还可以在PV中执行以下命令配置STS进行权限认证。

```
kubectl create -f pv-sts.yaml
```

以下为配置STS权限认证的pv-sts.yaml示例文件。

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-oss
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: ossplugin.csi.alibabacloud.com
    volumeHandle: pv-oss // 需要和PV名字一致。
    volumeAttributes:
      bucket: "oss"
      url: "oss-cn-hangzhou.aliyuncs.com"
      otherOpts: "-o max_stat_cache_size=0 -o allow_other"
      authType: "sts"
```

**步骤二：创建应用**

创建一个名为oss-static的应用并挂载PVC。

执行以下命令创建oss-static.yaml。

```
kubectl create -f oss-static.yaml
```

以下为创建应用oss-static.yaml示例文件。

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: oss-static
  labels:
    app: nginx
spec:
  replicas: 1
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
          - name: pvc-oss
            mountPath: "/data"
          - name: pvc-oss
            mountPath: "/data1"
        livenessProbe:
          exec:
            command:
            - sh
            - -c
            - cd /data
          initialDelaySeconds: 30
          periodSeconds: 30
      volumes:
        - name: pvc-oss
          persistentVolumeClaim:
            claimName: pvc-oss
```

-   `livenessProbe`：配置健康检查，详细介绍，请参见[OSS存储卷概述](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/OSS存储卷/OSS存储卷概述.md)。
-   `mountPath`：OSS在容器中挂载的位置。
-   `claimName`：PVC的名称，用于绑定PVC。

## 验证OSS的持久化存储

1.  查看部署oss-static应用的Pod和OSS文件。

    1.  执行以下命令，查看部署的oss-static应用所在Pod的名称。

        ```
        kubectl get pod
        ```

        预期输出：

        ```
        NAME                             READY   STATUS    RESTARTS   AGE
        oss-static-66fbb85b67-d****      1/1     Running   0          1h
        ```

    2.  执行以下命令，查看/data路径下的文件。

        ```
         kubectl exec oss-static-66fbb85b67-d**** ls /data | grep tmpfile
        ```

        无返回结果，说明/data路径下无文件。

2.  执行以下命令，在/data路径下创建文件static。

    ```
    kubectl exec oss-static-66fbb85b67-d**** touch /data/tmpfile
    ```

3.  执行以下命令，查看/data路径下的文件。

    ```
    kubectl exec oss-static-66fbb85b67-d**** ls /data | grep tmpfile
    ```

    预期输出：

    ```
    tmpfile
    ```

4.  执行以下命令，删除名称为`oss-static-66fbb85b67-d****`的Pod。

    ```
    kubectl delete pod oss-static-66fbb85b67-d****
    ```

    预期输出：

    ```
    pod "oss-static-66fbb85b67-d****" deleted
    ```

5.  同时在另一个窗口中，执行以下命令，查看Pod删除及重建Pod的过程。

    ```
    kubectl get pod -w -l app=nginx
    ```

    预期输出：

    ```
    NAME                            READY   STATUS            RESTARTS   AGE
    nginx-static-78c7dcb9d7-g****   2/2     Running           0          50s
    nginx-static-78c7dcb9d7-g****   2/2     Terminating       0          72s
    nginx-static-78c7dcb9d7-h****   0/2     Pending           0          0s
    nginx-static-78c7dcb9d7-h****   0/2     Pending           0          0s
    nginx-static-78c7dcb9d7-h****   0/2     Init:0/1          0          0s
    nginx-static-78c7dcb9d7-g****   0/2     Terminating       0          73s
    nginx-static-78c7dcb9d7-h****   0/2     Init:0/1          0          5s
    nginx-static-78c7dcb9d7-g****   0/2     Terminating       0          78s
    nginx-static-78c7dcb9d7-g****   0/2     Terminating       0          78s
    nginx-static-78c7dcb9d7-h****   0/2     PodInitializing   0          6s
    nginx-static-78c7dcb9d7-h****   2/2     Running           0          8s
    ```

6.  验证删除Pod后，存储空间里创建的文件是否还存在。

    1.  执行以下命令，查看重建的Pod名称。

        ```
        kubectl get pod
        ```

        预期输出：

        ```
        NAME                             READY   STATUS    RESTARTS   AGE
        oss-static-66fbb85b67-z****      1/1     Running   0          40s
        ```

    2.  执行以下命令，查看/data路径下的文件。

        ```
        kubectl exec oss-static-66fbb85b67-z**** ls /data | grep tmpfile
        ```

        预期输出：

        ```
        tmpfile
        ```

        temfile文件仍然存在，说明OSS的数据可持久化保存。


