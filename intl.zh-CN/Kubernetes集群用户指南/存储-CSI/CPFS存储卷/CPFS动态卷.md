---
keyword: [CPFS的数据存储, cpfs-provisioner, cpfs-plugin]
---

# CPFS动态卷

CPFS（Cloud Paralleled File System）是一种并行文件系统。CPFS的数据存储在集群中的多个数据节点，并可由多个客户端同时访问，从而能够为大型高性能计算机集群提供高IOPS、高吞吐、低时延的数据存储服务。本文介绍如何挂载及使用CPFS存储卷。

已开通CPFS服务。

若未开通CPFS服务，请[提交工单](https://workorder-intl.console.aliyun.com/console.htm)联系CPFS团队添加白名单。

阿里云容器服务Kubernetes（ACK） CSI支持静态存储卷挂载和动态存储卷挂载两种方式挂载CPFS存储卷。在静态存储卷挂载的方式中，通常需要手动编辑和创建一个PV和PVC进行挂载，当需要的PV和PVC数量很大的时候，手动创建就显得非常繁琐了，这时动态存储卷挂载的功能可以满足您的需求。本文介绍如何在ACK集群中使用alibaba-cloud-csi-driver挂载CPFS存储卷。具体操作，请参见[alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver)。

**说明：** 容器服务ACK的CPFS存储卷服务目前只支持CPFS 1.0版，不支持2.0版本。

## 操作步骤

1.  部署CSI-CPFS组件。

    如果您需要挂载CPFS存储卷，需要按照以下步骤在ACK集群中先部署cpfs-provisioner和cpfs-plugin组件。

    -   部署cpfs-provisioner。

        ```
        kind: Deployment
        apiVersion: apps/v1
        metadata:
          name: csi-cpfs-provisioner
          namespace: kube-system
        spec:
          selector:
            matchLabels:
              app: csi-cpfs-provisioner
          replicas: 2
          template:
            metadata:
              labels:
                app: csi-cpfs-provisioner
            spec:
              tolerations:
              - operator: "Exists"
              affinity:
                nodeAffinity:
                  preferredDuringSchedulingIgnoredDuringExecution:
                  - weight: 1
                    preference:
                      matchExpressions:
                      - key: node-role.kubernetes.io/master
                        operator: Exists
              priorityClassName: system-node-critical
              serviceAccount: admin
              hostNetwork: true
              containers:
                - name: external-cpfs-provisioner
                  image: registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.6.0-e360c7e43-aliyun
                  args:
                    - "--provisioner=cpfsplugin.csi.alibabacloud.com"
                    - "--csi-address=$(ADDRESS)"
                    - "--volume-name-prefix=cpfs"
                    - "--timeout=150s"
                    - "--enable-leader-election=true"
                    - "--leader-election-type=leases"
                    - "--retry-interval-start=500ms"
                    - "--v=5"
                  env:
                    - name: ADDRESS
                      value: /socketDir/csi.sock
                  imagePullPolicy: "Always"
                  volumeMounts:
                    - name: socket-dir
                      mountPath: /socketDir
              volumes:
                - name: socket-dir
                  hostPath:
                    path: /var/lib/kubelet/csi-plugins/cpfsplugin.csi.alibabacloud.com
                    type: DirectoryOrCreate
        ```

    -   部署csi-plugin。

        ```
        apiVersion: storage.k8s.io/v1
        kind: CSIDriver
        metadata:
          name: cpfsplugin.csi.alibabacloud.com
        spec:
          attachRequired: false
          podInfoOnMount: true
        ---
        # This YAML defines all API objects to create RBAC roles for csi node plugin.
        kind: DaemonSet
        apiVersion: apps/v1
        metadata:
          name: csi-cpfsplugin
          namespace: kube-system
        spec:
          selector:
            matchLabels:
              app: csi-cpfsplugin
          template:
            metadata:
              labels:
                app: csi-cpfsplugin
            spec:
              tolerations:
              - operator: Exists
              priorityClassName: system-node-critical
              serviceAccount: admin
              hostNetwork: true
              hostPID: true
              containers:
              - name: driver-registrar
                image: registry.cn-hangzhou.aliyuncs.com/acs/csi-node-driver-registrar:v1.2.0
                imagePullPolicy: Always
                args:
                - "--v=5"
                - "--csi-address=/var/lib/kubelet/csi-plugins/cpfsplugin.csi.alibabacloud.com/csi.sock"
                - "--kubelet-registration-path=/var/lib/kubelet/csi-plugins/cpfsplugin.csi.alibabacloud.com/csi.sock"
                volumeMounts:
                - name: kubelet-dir
                  mountPath: /var/lib/kubelet/
                - name: registration-dir
                  mountPath: /registration
              - name: csi-cpfsplugin
                securityContext:
                  privileged: true
                  capabilities:
                    add: ["SYS_ADMIN"]
                  allowPrivilegeEscalation: true
                image: registry.cn-hangzhou.aliyuncs.com/acs/csi-cpfsplugin:v1.16.6-d539237c
                imagePullPolicy: "Always"
                args:
                - "--endpoint=$(CSI_ENDPOINT)"
                - "--v=5"
                - "--driver=cpfsplugin.csi.alibabacloud.com"
                env:
                - name: CSI_ENDPOINT
                  value: unix://var/lib/kubelet/csi-plugins/cpfsplugin.csi.alibabacloud.com/csi.sock
                volumeMounts:
                - name: kubelet-dir
                  mountPath: /var/lib/kubelet/
                  mountPropagation: "Bidirectional"
                - mountPath: /var/log/
                  name: host-log
                - name: etc
                  mountPath: /host/etc
              volumes:
              - name: kubelet-dir
                hostPath:
                  path: /var/lib/kubelet/
                  type: Directory
              - name: registration-dir
                hostPath:
                  path: /var/lib/kubelet/plugins_registry
                  type: DirectoryOrCreate
              - name: host-log
                hostPath:
                  path: /var/log/
              - name: etc
                hostPath:
                  path: /etc
          updateStrategy:
            type: RollingUpdate
        ```

2.  使用CPFS动态卷。

    1.  使用以下模板创建StorageClass。

        ```
        apiVersion: storage.k8s.io/v1
        kind: StorageClass
        metadata:
          name: alicloud-cpfs
        parameters:
          volumeAs: subpath
          server: "cpfs-0237cc41-**.cn-shenzhen.cpfs.nas.aliyuncs.com@tcp:cpfs-0237cc41-**.cn-shenzhen.cpfs.nas.aliyuncs.com@tcp:/0237cc41"
          archiveOnDelete: "false"
        provisioner: cpfsplugin.csi.alibabacloud.com
        reclaimPolicy: Delete
        ```

        |参数|说明|
        |--|--|
        |volumeAs|定义动态卷的类型。目前只支持subpath类型；标志创建的PV实质是CPFS服务器上的一个子目录。|
        |server|定义CPFS服务器地址。格式为`挂载点地址@tcp:挂载点地址@tcp:/subpath`。|
        |archiveOnDelete|表示删除PVC、PV时候，处理CPFS子目录的方式：         -   如果reclaimPolicy为Delete，且archiveOnDelete为false：会直接删除远端目录和数据，请谨慎使用。
        -   如果reclaimPolicy为Delete，且archiveOnDelete为true：会将远端的目录更新为其他名字备份。
        -   如果reclaimPolicy为Retain，远端的目录不作处理。 |

    2.  使用以下模板创建PVC。

        ```
        kind: PersistentVolumeClaim
        apiVersion: v1
        metadata:
          name: cpfs-pvc
        spec:
          accessModes:
            - ReadWriteMany
          storageClassName: alicloud-cpfs
          resources:
            requests:
              storage: 20Gi
        ```

        **说明：** PVC中要指定使用的storageClassName。

    3.  使用以下模板创建应用。

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: deployment-cpfs
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
                  - name: cpfs-pvc
                    mountPath: "/data"
              volumes:
                - name: cpfs-pvc
                  persistentVolumeClaim:
                    claimName: cpfs-pvc
        ```


