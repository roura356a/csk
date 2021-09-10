---
keyword: [CNFS, 自动扩容, NAS存储卷]
---

# 使用CNFS自动扩容NAS存储卷

通过定义CNFS中托管的NAS存储卷的扩容策略，可在NAS存储卷的使用率高于某个阈值时触发自动扩容。本文介绍如何使用CNFS实现NAS存储卷的自动扩容。

-   已创建Kubernetes集群，且存储插件选择为CSI。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
    -   若需要新建集群，选择CSI存储插件时请同时选中**创建默认NAS文件系统和CNFS容器网络文件系统动态存储类型**。
    -   若已创建集群，但未选中**创建默认NAS文件系统和CNFS容器网络文件系统动态存储类型**，请使用CNFS托管NAS文件系统。具体操作，请参见[使用CNFS托管NAS文件系统](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/容器网络文件系统/使用CNFS托管NAS文件系统.md)。
-   csi-plugin和csi-provisioner组件版本不低于v1.20.5-ff6490f-aliyun。关于升级CSI-Plugin和CSI-Provisioner组件的操作，请参见[升级CSI-Plugin和CSI-Provisioner](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/安装与升级CSI组件.md)。
-   storage-operator组件版本不低于v1.18.8.56-2aa33ba-aliyun。关于升级storage-operator组件的操作，请参见[管理组件](/intl.zh-CN/Kubernetes集群用户指南/组件/管理组件.md)。
-   已通过kubectl工具连接Kubernetes集群。具体操作，请参见[通过kubectl工具连接集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

## 步骤一：配置NAS自动扩容策略

1.  查看CNFS对象的状态。

    需要确保CNFS对象的状态为**Available**。

    1.  执行以下命令查看CNFS对象。

        ```
        kubectl get cnfs
        ```

        预期输出：

        ```
        NAME                                      AGE
        default-cnfs-nas-837d6ea-20210819155623   14d
        ```

    2.  执行以下命令查看CNFS对象的状态。

        ```
        kubectl get cnfs default-cnfs-nas-837d6ea-20210819155623 -o yaml | grep Available
        ```

        预期输出：

        ```
        status: Available
        ```

2.  使用以下YAML模板创建NAS扩容策略。

    ```
    cat << EOF | kubectl apply -f -
    apiVersion: storage.alibabacloud.com/v1alpha1
    kind: StorageAutoScalerPolicy
    metadata:
      name: hybrid-expand-policy
    spec:
      pvcSelector:
        matchLabels:
          app: nginx      #需要与PVC及Deployment下labels的配置相同，本示例均为app: nginx。
      namespaces:
        - default
        - nginx
      conditions:
        - name: condition1
          key: volume-capacity-used-percentage
          operator: Gt
          values:
            - "80"
      actions:
        - name: action1
          type: volume-expand
          params:
            scale: 100%
            limits: 500Gi
    EOF
    ```

    |参数|说明|
    |--|--|
    |pvcSelector|选择目标PVC，通过Label-Selector方式实现扩容策略与PVC之间的匹配。本示例为nginx。|
    |namespaces|表示目标PVC所在的命名空间，多个命名空间为或逻辑。若不配置，默认为default。本示例为default及nginx。|
    |conditions|表示触发规则的条件，多个condition为与逻辑。每个condition包含以下参数：    -   key：定义一个Metric的类型。
    -   volume-capacity-used-percentage：表示容量使用百分比。
    -   operator：定义规则，包含Gt（大于）、Lt（小于）、Eq（等于）或Ne（不等于），不限制英文字母大小写。
    -   values：规则的具体数值。
本示例表示当PVC容量的使用率高于80%时会触发actions。 |
    |actions|表示满足上述conditions时执行的操作，可以是多个操作。包含以下参数：    -   type：表示行为方式，目前只支持扩容。
    -   scale：表示扩容的大小，单位为GiB，或可使用百分比。
    -   limits：表示PVC在此action中的最大限制。
若actions中有多个action时，则从首个action开始匹配，执行首个满足条件的action，其余跳过。

本示例的action1表示，当满足扩容条件时NAS存储卷容量以当前容量的100%为单位扩容，最大扩容到500 GiB。 |

3.  使用以下YAML模板创建PVC及Deployment。

    **说明：** 通过匹配扩容策略与对应PVC及Deployment之间的标签，将扩容策略应用到PVC及Deployment中。例如，本示例扩容策略中pvcSelector.matchLabels的值为`app: nginx`，对应PVC及Deployment中labels的值为`app: nginx`。

    ```
    cat << EOF | kubectl apply -f -
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: cnfs-nas-pvc
      labels:
        app: nginx   #需要与扩容策略YAML模板下pvcSelector.matchLabels的配置相同，本示例均为app: nginx。
    spec:
      accessModes:
        - ReadWriteMany
      storageClassName: alibabacloud-cnfs-nas
      resources:
        requests:
          storage: 50Gi
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: cnfs-nas-deployment
      labels:
        app: nginx
    spec:
      selector:
        matchLabels:
          app: nginx   #需要与扩容策略YAML模板下pvcSelector.matchLabels的配置相同，本示例均为app: nginx。
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.14.2
            volumeMounts:
            - mountPath: "/data"
              name: cnfs-nas-pvc
          volumes:
          - name: cnfs-nas-pvc
            persistentVolumeClaim:
              claimName: cnfs-nas-pvc
    EOF
    ```


## 步骤二：验证NAS存储卷的自动扩容

1.  执行以下命令查看命名空间**default**下的所有Pod。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                                                            READY   STATUS    RESTARTS   AGE
    cnfs-nas-deployment-56dbcc7fb7-wh79z   1/1      Running            0          20m
    ```

2.  执行以下命令进入应用**cnfs-nas-deployment-56dbcc7fb7-wh79z**。

    ```
    kubectl exec cnfs-nas-deployment-56dbcc7fb7-wh79z -ti sh
    ```

3.  执行以下命令在NAS存储卷挂载目录写入50 GiB的测试数据。

    ```
    cd /data
    dd if=<数据路径> of=<挂载路径>
    ```

4.  执行以下命令查看触发扩容的事件。

    ```
    kubectl get events
    ```

    预期输出：

    ```
    default    0s    Warning   NotEnoughDiskSpace           persistentvolumeclaim/cnfs-nas-pvc              Pvc cnfs-nas-pvc is not enough disk space, namespace: default, totalSize:50Gi, usedSize:49Gi, usedPercentage:98.00%, threshold:85.00%
    default    1s    Warning   StartExpand                  persistentvolumeclaim/cnfs-nas-pvc              Start to expand of pvc cnfs-nas-pvc from 50Gi to 100Gi, usedCapacityPercentage:98%, freeSize:1024MB.
    default    0s    Normal    Resizing                     persistentvolumeclaim/cnfs-nas-pvc              External resizer is resizing volume nas-1acba306-e626-46f3-8441-110c10a6****
    default    0s    Warning   ExternalExpanding            persistentvolumeclaim/cnfs-nas-pvc              Ignoring the PVC: didn't find a plugin capable of expanding the volume; waiting for an external controller to process this PVC.
    default    0s    Normal    FileSystemResizeRequired     persistentvolumeclaim/cnfs-nas-pvc              Require file system resize of volume on node
    default    0s    Normal    FileSystemResizeSuccessful   pod/cnfs-nas-deployment-56dbcc7fb7-wh79z        MountVolume.NodeExpandVolume succeeded for volume "nas-1acba306-e626-46f3-8441-110c10a6****"
    ```

    从以上预期输出可得，当前NAS存储卷的容量使用量已超过80%，触发扩容，NAS存储卷容量将从50 GiB扩容到100 GiB。

5.  在CSI Nodes监控大盘查看CNFS监控信息。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群管理页左侧导航栏中，选择**运维管理** \> **Prometheus监控**。

    5.  在Prometheus监控页面，单击**CSI Nodes**页签。

    6.  在CSI Nodes页签，选择NAS存储卷所在的**StorageType**、**Namespace**及PVC名称后，在**Total Capacity**区域可查看NAS存储卷当前容量。

        本示例**StorageType**选择为**nas**、**Namespace**选择为**default**、**PVC**为**cnfs-nas-pvc**。

        ![NAS存储卷容量](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9306890361/p324034.png)

        从上图可看到，在16:55:30触发扩容，且扩容后容量为100 GiB。


