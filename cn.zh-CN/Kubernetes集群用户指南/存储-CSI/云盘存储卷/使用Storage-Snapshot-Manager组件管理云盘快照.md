---
keyword: [Storage-Snapshot-Manager组件, 云盘快照]
---

# 使用Storage-Snapshot-Manager组件管理云盘快照

Kubernetes官方组件csi external-snapshotter只支持最基本快照的创建及删除，而Storage-Snapshot-Manager对官方组件功能进行了扩展，满足云盘快照的更多需求。目前主要支持云盘定时快照、组快照功能。本文介绍如何配置storage-snapshot-manager组件，及云盘定时快照、组快照功能。

-   您已创建一个ACK集群，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   请确保您集群的Kubernetes版本不低于1.18。
-   您已通过kubectl连接集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

**说明：**

-   默认情况下，每个集群中最多可包含100个节点。如果您需要添加更多节点，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)申请。
-   添加已有云服务器时，请确保您的专有网络中的ECS实例绑定一个弹性公网IP（EIP），或者相应VPC已经配置了NAT网关。您需要确保相应节点能正常访问公网，否则，添加云服务器会失败。

## 架构图

![Storage Snapshot Manager](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8426706161/p251382.png)

-   VolumeSnapshotPolicy是一个CRD，需要在使用前部署在集群里面，该CRD对云盘快照的生成策略进行了详细的定义。
-   StorageSnapshotManager以Deployment形式部署在Kubernetes集群中kube-system的命名空间下，负责监听VolumeSnapshotPolicy、PVC、Pod等资源。访问ECS的Openapi对定时快照进行创建，以及在集群内创建VolumeSnapshot资源。

## 通过控制台自动配置storage-snapshot-manager组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **组件管理**。

5.  单击存储页签，在storage-operator组件区域单击**安装**。

    控制台会通过storage-operator自动配置storage-snapshot-manager组件。


## 通过命令配置storage-snapshot-manager组件

**步骤一：部署storage-snapshot-manager组件**

1.  执行以下命令创建Deployment。

    ```
    kubectl apply -f deployment.yaml
    ```

    Deployment模板示例如下所示：

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: storage-snapshot-manager
      namespace: kube-system
      labels:
        app: storage-snapshot-manager
    spec:
      selector:
        matchLabels:
          app: storage-snapshot-manager
      template:
        metadata:
          labels:
            app: storage-snapshot-manager
        spec:
          tolerations:
            - operator: "Exists"
          priorityClassName: system-node-critical
          serviceAccount: admin
          hostNetwork: true
          hostPID: true
          containers:
          - name: storage-snapshot-manager
            image: registry.${regionId}.aliyuncs.com/acs/storage-snapshot-manager:v1.18.8.0-81508da-aliyun
            imagePullPolicy: Always
            env:
            - name: SNAPSHOT_CLASS
              value: ""
            volumeMounts:
            - name: date-config
              mountPath: /etc/localtime
          volumes:
          - name: date-config
            hostPath:
              path: /etc/localtime
    ```

2.  执行以下命令查看storage-snapshot-manager组件是否启动。

    ```
    kubectl get pods -nkube-system | grep storage-snapshot-manager | grep Running
    ```

    预期输出：

    ```
    NAME         READY   STATUS     RESTARTS   AGE         IP                NODE              NOMINATED NODE   READINESS GATES
    storage-***  1/1     Running    0          6d17h       192.168.XX.XX     virtual-kubelet   <none>           <none>
    ```


**步骤二：部署VolumeSnapshotClass实例**

1.  执行以下命令部署VolumeSnapshotClass实例。

    ```
    kubectl apply -f volume-snapshot-class.yaml
    ```

    VolumeSnapshotClass实例模板示例如下所示：

    ```
    apiVersion: snapshot.storage.k8s.io/v1beta1 
    deletionPolicy: Delete 
    driver: diskplugin.csi.alibabacloud.com 
    kind: VolumeSnapshotClass 
    metadata: 
      name: default-snapclass 
    parameters: 
      retentionDays: "1" 
    ```

2.  执行以下命令验证storage-operator是否正常创建。

    ```
    kubectl get sc | grep default-snapclass
    ```

    预期输出：

    ```
    NAME                DRIVER                            DELETIONPOLICY   AGE
    default-snapclass   diskplugin.csi.alibabacloud.com   Delete           9d
    ```

    当提示以上类似信息时表示storage-operator已创建成功。


**步骤三：部署VolumeSnapshotPolicy CRD**

1.  执行以下命令创建CRD。

    ```
    kubectl create -f volumesnapshotcrd.yaml
    ```

    创建CRD的模板示例如下所示：

    ```
    apiVersion: apiextensions.k8s.io/v1
    kind: CustomResourceDefinition
    metadata:
      name: volumesnapshotpolicies.storage.alibabacloud.com
    spec:
      group: storage.alibabacloud.com
      versions:
      - name: v1beta1
        served: true
        storage: true
        schema:
          openAPIV3Schema:
            description: VolumeSnapshotPolicy is the Schema for the VolumeSnapshotPolicy API
            properties:
              apiVersion:
                description: 'APIVersion defines the versioned schema of this representation
                        of an object. Servers should convert recognized schemas to the latest
                        internal value, and may reject unrecognized values. More info: https://git.k8s.io/community/contributors/devel/api-conventions.md#resources'
                type: string
              kind:
                description: 'Kind is a string value representing the REST resource this
                        object represents. Servers may infer this from the endpoint the client
                        submits requests to. Cannot be updated. In CamelCase. More info: https://git.k8s.io/community/contributors/devel/api-conventions.md#types-kinds'
                type: string
              metadata:
                type: object
              spec:
                description: VolumeSnapshotPolicySpec defines the desired Specification of VolumeSnapshotPolicy
                properties:
                  pvcSelector:
                    description: pvcSelector select pvcs which suppose to apply VolumeSnapshotPolicy 
                    type: object
                    properties:
                      matchLabels:
                        x-kubernetes-preserve-unknown-fields: true
                        description: matchLabels select multi pvcs by match pvc labels 
                        type: object
                  volumeSnapshotNamespaces:
                    description: volumeSnapshotNamespaces defines which namespace should VolumeSnapshot be created
                    type: array
                    items:
                      type: string
                  retentionDays:
                    description: retentionDays is days to save snapshot
                    format: int64
                    type: integer
                  snapshotSchedule:
                    type: object
                    description: snapshotSchedule defines generate rules that how to generate VolumeSnapshot 
                    properties:
                      repeatOnce:
                        description: repeatOnce defines only generate snapshot once
                        type: boolean
                      repeatWeekdays:
                        description: RepeatWeekdays is a list of days in a week to create disk snapshot
                        type: array
                        items:
                            type: string
                      timePoints:
                        description: TimePoints is a list of hours in a day to create disk snapshot
                        type: array
                        items:
                            type: string
                type: object
            type: object
        subresources:
          status: {}
      scope: Cluster
      names:
        kind: VolumeSnapshotPolicy
        plural: volumesnapshotpolicies
        shortNames:
        - vsp
    ```

2.  执行以下命令检查CRD是否已正确创建。

    ```
    kubectl get crd volumesnapshotpolicies.storage.alibabacloud.com
    ```

    预期输出：

    ```
    NAME                                                 CREATED AT
    volumesnapshotpolicies.storage.alibabacloud.com      2021-03-09T03:26:42Z
    ```

    当提示以上类似信息时表示CRD已创建成功。


**步骤四：添加云产品访问权限**

对于托管版集群，请按照以下步骤操作。

1.  使用具有RAM权限的账号登录[RAM管理控制台](https://ram.console.aliyun.com/)。

2.  单击左侧导航栏的**RAM角色管理**。

3.  在RAM角色管理页面查询AliyunCSManagedCsiRole。

4.  单击列表中**RAM角色名称**字段**AliyunCSManagedCsiRole**的链接。

5.  在RAM角色基本信息的**权限管理**页签，单击**权限策略名称**字段**AliyunCSManagedCsiRolePolicy**的链接。

6.  在AliyunCSManagedCsiRolePolicy页面的策略内容页签，将以下内容添加到策略内容中。

    ```
    {
                "Effect": "Allow",
                "Action": [
                    "ecs:DescribeInstances",
                    "ecs:CreateAutoSnapshotPolicy",
                    "ecs:DeleteAutoSnapshotPolicy",
                    "ecs:DescribeSnapshots",
                    "ecs:ApplyAutoSnapshotPolicy",
                    "ecs:ModifyAutoSnapshotPolicy",
                    "ecs:DescribeAutoSnapshotPolicyEX"
                ],
                "Resource": [
                    "*"
                ],
                "Condition": {}
    }
    ```


对于专有版集群，请按照以下步骤操作。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在**集群信息**页面，单击**集群资源**页签，单击**Worker RAM角色**右侧链接。

5.  在RAM角色基本信息的**权限管理**页签，单击目标**权限策略名称**的链接。

6.  单击**修改策略内容**。

7.  在修改策略内容面板增加以下内容后，单击**确定**。

    ```
    {
                "Effect": "Allow",
                "Action": [
                    "ecs:DescribeInstances",
                    "ecs:CreateAutoSnapshotPolicy",
                    "ecs:DeleteAutoSnapshotPolicy",
                    "ecs:DescribeSnapshots",
                    "ecs:ApplyAutoSnapshotPolicy",
                    "ecs:ModifyAutoSnapshotPolicy",
                    "ecs:DescribeAutoSnapshotPolicyEX"
                ],
                "Resource": [
                    "*"
                ],
                "Condition": {}
    }
    ```


## 创建云盘快照及快照策略管理

storage-snapshot-manager插件启动后，系统会自动检查当前集群中是否存在VolumeSnapshotPolicy实例。

1.  创建PV。

    以下为创建PV的YAML示例文件。

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: csi-pv-snapshot-policy
      labels:
        app: nginx
        alicloud-pvname: static-disk-pv-snapshot-policy
    spec:
      capacity:
        storage: 25Gi
      accessModes:
        - ReadWriteOnce
      persistentVolumeReclaimPolicy: Retain
      csi:
        driver: diskplugin.csi.alibabacloud.com
        volumeHandle: <your-disk-id>
    ```

2.  创建PVC。

    以下为创建PVC的YAML示例文件。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: csi-pvc-snapshot-policy
      labels:
        app: nginx
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 25Gi
      selector:
        matchLabels:
          alicloud-pvname: static-disk-pv-snapshot-policy
    ```

3.  创建Pod并关联创建的PV及PVC。

    以下为创建Pod的YAML示例文件。

    ```
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web-policy
    spec:
      selector:
        matchLabels:
          app: nginx-policy
      serviceName: "nginx"
      template:
        metadata:
          labels:
            app: nginx-policy
        spec:
          containers:
          - name: nginx-policy
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
                claimName: csi-pvc-snapshot-policy
    ```

    当Pod启动之后，storage-snapshot-manager组件会自动将PV对应的<your-disk-id\>与VolumeSnapshotPolicy进行关联，并按照策略进行快照生成。

4.  创建VolumeSnapshotPolicy实例。

    -   组快照类型实例。

        以下为创建组快照类型VolumeSnapshotPolicy实例的YAML示例文件。

        ```
        apiVersion: v1 
        items: 
          - apiVersion: storage.alibabacloud.com/v1beta1 
        kind: VolumeSnapshotPolicy 
        metadata: 
          name: volumesnapshotpolicy1 
        spec: 
          pvcSelector: 
            matchLabels: 
              app: "nginx" 
              snapshotSchedule: 
                repeatOnce: true 
                kind: List 
                metadata: resource
                Version: "" 
                selfLink: "" 
        ```

        **说明：**

        -   pvcSelector：快照策略覆盖的PVC，通过PVC Labels指定。
        -   snapshotSchedule.repeatOnce：只为指定的云盘创建一次快照。
    -   定时快照类型实例。

        以下为创建定时快照类型VolumeSnapshotPolicy实例的YAML示例文件。

        ```
        apiVersion: v1 
        items: 
          - apiVersion: storage.alibabacloud.com/v1beta1 
        kind: VolumeSnapshotPolicy 
        metadata: 
          name: volumesnapshotpolicy2 
        spec: 
          pvcSelector: 
            matchLabels: 
              app: "nginx1" 
              retentionDays: 30 
              snapshotSchedule: 
                repeatWeekdays: ["1", "2", "4", "5"] 
                timePoints: ["2", "10", "12", "16"] 
                kind: List 
                metadata: resource
                Version: "" 
                selfLink: "" 
        ```

        **说明：**

        -   pvcSelector：快照策略覆盖的PVC，通过PVC Labels指定。
        -   retentionDays：自动快照创建保留天数，当为`-1`时表示永久保存。repeatOnce不支持该参数。
        -   snapshotSchedule.repeatWeekdays：一周内自动创建快照的时间点（天），可设置为\[1~7\]。
        -   snapshotSchedule.timePoints：一天内自动创建快照的时间点（小时），可设置为 \[0~23\]。
        在集群创建VolumeSnapshotPolicy实例时，系统会在对应ECS服务上自动创建快照策略。

5.  查看定时快照策略是否生效。

    1.  登录[ECS管理控制台](https://ecs.console.aliyun.com)。

    2.  在左侧导航栏，选择**存储与快照** \> **快照**。

    3.  单击自动快照策略页签，查看快照策略是否已关联执行云盘。

        ![自动快照策略](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9763216161/p253173.png)

6.  修改快照策略。

    **说明：**

    -   修改定时快照策略会影响该策略关联的所有云盘，请谨慎修改。
    -   建议不要在ECS管理控制台进行策略修改，所有的修改请通过CRD操作。
    -   组快照策略不会在ECS管理控制台上显示。
    1.  执行以下命令通过VolumeSnapshotPolicy CRD修改快照策略。

        ```
        kubectl edit volumesnapshotpolicy volumesnapshotpolicy1
        ```

    2.  在ECS管理控制台查看快照策略是否已更新。


## 使用生成的快照进行磁盘恢复

绑定定时快照策略之后，会在集群中看到自动创建的快照。

1.  执行以下命令查看集群中创建的快照。

    ```
    kubectl get volumesnapshot
    ```

    预期输出：

    ```
    NAME          READYTOUSE   RESTORESIZE   DELETIONPOLICY   DRIVER                            VOLUMESNAPSHOTCLASS   VOLUMESNAPSHOT           AGE
    s-uf6221***   true         41943040      Delete           diskplugin.csi.alibabacloud.com   default-snapclass     s-uf622145z6iibqtlrbwi   7m40s
    s-uf65y0***   true         41943040      Delete           diskplugin.csi.alibabacloud.com   default-snapclass     s-uf65y0zrhwsd581q60mg   7m40s
    s-uf6a83***   true         41943040      Delete           diskplugin.csi.alibabacloud.com   default-snapclass     s-uf6a83009o5s2jgcch9f   7m40s
    s-uf6fmp***   true         41943040      Delete           diskplugin.csi.alibabacloud.com   default-snapclass     s-uf6fmpbyrlm10amjicha   7m40s
    ```

2.  使用任意一个volumesnapshot进行云盘的恢复。

    以`s-uf6221***`为例：

    ```
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web-restore
    spec:
      selector:
        matchLabels:
          app: nginx
      serviceName: "nginx"
      replicas: 1
      template:
        metadata:
          labels:
            app: nginx
        spec:
          hostNetwork: true
          containers:
          - name: nginx
            image: nginx
            command: ["sh", "-c"]
            args: ["sleep 10000"]
            volumeMounts:
            - name: disk-ssd
              mountPath: /data
      volumeClaimTemplates:
      - metadata:
          name: disk-ssd
        spec:
          accessModes: [ "ReadWriteOnce" ]
          storageClassName: alicloud-disk-ssd
          resources:
            requests:
              storage: 20Gi
          dataSource:
            name: s-uf6221***
            kind: VolumeSnapshot
            apiGroup: snapshot.storage.k8s.io
    ```

    等待Pod启动，即完成定时快照中数据的恢复。


## 常见问题



