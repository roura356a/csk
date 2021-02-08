---
keyword: [OSS, CSI, 持久化存储]
---

# 使用OSS实现持久化存储-CSI

当容器发生宕机故障时，有状态服务容器存储的业务数据存在着丢失和不可靠等风险。使用持久化存储可以解决该问题。本文介绍如何使用OSS实现持久化存储。

-   已创建ACK集群。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。
-   已创建Bucket。具体操作，请参见[创建存储空间](/intl.zh-CN/快速入门/控制台快速入门/创建存储空间.md)。

阿里云对象存储服务（OSS）提供海量、安全、低成本、高可靠的云存储服务。OSS支持同时被多个Pod挂载。以下为OSS的使用场景：

-   对磁盘I/O要求低。
-   配置文件、图片、小视频等共享业务。

## 注意事项

-   OSS不支持动态创建PV。
-   容器服务Kubernetes集群升级会重启Kubelet，OSSFS驱动跟随一起重启，导致OSS目录不可用。这时使用OSS的Pod需要重建，可在YAML文件中增加健康检查的配置，在容器内OSS目录不可用时自动重启Pod，重新挂载OSS。

    **说明：** 如果您使用的是v1.18.8.45及以上版本的csi-plugin和csi-provisioner插件，则不会出现上述问题。


## 步骤一：创建PV

1.  使用以下内容，创建pv-oss.yam。

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
    ```

    |参数|说明|
    |--|--|
    |`name`|PV的名称。|
    |`labels`|配置PV的标签。|
    |`storage`|OSS的可使用量。|
    |`accessModes`|配置访问模式。|
    |`persistentVolumeReclaimPolicy`|PV回收策略。|
    |`driver`|定义驱动类型。取值为ossplugin.csi.alibabacloud.com，表示使用OSS CSI插件。|
    |`nodePublishSecretRef`|定义挂载PV时通过Secret对象来获取AccessKey信息。|
    |`volumeHandle`|配置PV的名称。|
    |`bucket`|需要挂载的OSS Bucket。|
    |`url`|挂载OSS的接入域名，挂载节点和Bucket相同region时，可使用内网地址。|
    |`otherOpts`|挂载OSS时支持输入定制化参数，格式为：-o \*\*\* -o \*\*\*。|

2.  执行以下命令，创建PV。

    ```
     kubectl create -f pv-oss.yaml
    ```

    登录[容器服务管理控制台](https://cs.console.aliyun.com)[容器服务管理控制台](https://partners-intl.console.aliyun.com/#/cs)。在控制台左侧导航栏中，单击**集群**。在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。在集群管理页左侧导航栏选择**存储** \> **存储卷**。可以在存储卷页面查看到创建的PV。


## 步骤二：创建PVC

创建OSS存储声明PVC，可以使用selector筛选PV，精确配置PVC和PV的绑定关系。

1.  使用以下内容，创建pvc-oss.yaml。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: pvc-oss
    spec:
      accessModes:
        - ReadWriteMany
      storageClassName: oss
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
    |`storageClassName`|本例设置为OSS，表示PVC只与OSS类型的PV绑定。|
    |`storage`|声明应用使用量，不能大于存储卷的总量。|
    |`alicloud-pvname`|通过标签关联PV，与PV标签保持一致。|

2.  执行以下命令，创建PVC。

    ```
    kubectl create -f pvc-oss.yaml
    ```

    在集群管理页左侧导航栏选择**存储** \> **存储声明**，在存储声明页面可以看到创建的PVC。


## 步骤三：创建应用

1.  使用以下内容，创建oss-static.yaml。

    创建一个名为oss-static的应用并挂载PVC。

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

    -   `livenessProbe`：配置健康检查，详细介绍，请参见[OSS卷使用说明](/intl.zh-CN/Kubernetes集群用户指南/存储管理-CSI/OSS存储卷/OSS卷使用说明.md)。
    -   `mountPath`：OSS在容器中挂载的位置。
    -   `claimName`：PVC的名称，用于绑定PVC。
2.  执行以下命令，创建应用。

    ```
    kubectl create -f oss-static.yaml
    ```


## 验证OSS的持久化存储

1.  查看部署oss-static应用的Pod和OSS文件。

    1.  执行以下命令，查看部署的oss-static应用所在Pod的名称。

        ```
        kubectl get pod
        ```

        预期输出：

        ```
        NAME                             READY   STATUS    RESTARTS   AGE
        oss-static-66fbb85b67-dqbl2      1/1     Running   0          1h
        ```

    2.  执行以下命令，查看/data路径下的文件。

        ```
         kubectl exec oss-static-66fbb85b67-dqbl2 ls /data | grep tmpfile
        ```

        无返回结果，说明/data路径下无文件。

2.  执行以下命令，在/data路径下创建文件static。

    ```
    kubectl exec oss-static-66fbb85b67-dqbl2 touch /data/tmpfile
    ```

3.  执行以下命令，查看/data路径下的文件。

    ```
    kubectl exec oss-static-66fbb85b67-dqbl2 ls /data | grep tmpfile
    ```

    预期输出：

    ```
    tmpfile
    ```

4.  执行以下命令，删除名称为`oss-static-66fbb85b67-d****`的Pod。

    ```
    kubectl delete pod oss-static-66fbb85b67-dqbl2
    ```

    预期输出：

    ```
    pod "oss-static-66fbb85b67-dqbl2" deleted
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

6.  验证删除Pod后，云盘里创建的文件是否还存在。

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
        kubectl exec oss-static-66fbb85b67-zlvmw ls /data | grep tmpfile
        ```

        预期输出：

        ```
        tmpfile
        ```

        temfile文件仍然存在，说明OSS的数据可持久化保存。


