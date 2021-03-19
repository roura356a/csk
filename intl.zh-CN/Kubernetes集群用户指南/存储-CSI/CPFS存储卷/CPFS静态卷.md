---
keyword: [cpfs-plugin, CPFS的数据存储]
---

# CPFS静态卷

CPFS（Cloud Paralleled File System）是一种并行文件系统。CPFS的数据存储在集群中的多个数据节点，并可由多个客户端同时访问，从而能够为大型高性能计算机集群提供高IOPS、高吞吐、低时延的数据存储服务。本文演示如何挂载及使用CPFS存储卷。

阿里云容器服务Kubernetes（ACK） CSI支持静态存储卷挂载和动态存储卷挂载两种方式挂载CPFS存储卷。在静态存储卷挂载的方式中，通常需要手动编辑和创建一个PV和PVC进行挂载，当需要的PV和PVC数量很大的时候，手动创建就显得非常繁琐了，这时动态存储卷挂载的功能可以满足您的需求。本文演示如何在ACK集群中使用alibaba-cloud-csi-driver挂载CPFS存储卷，请参见[alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver)。

**说明：** ACK CPFS存储卷服务目前只支持CPFS 1.0版，不支持2.0版本。

## 操作步骤

1.  部署CSI-CPFS组件。

    如果您需要挂载CPFS存储卷，需要按照以下步骤在ACK集群中先部署cpfs-plugin组件。

    ```
    apiVersion: storage.k8s.io/v1beta1
    kind: CSIDriver
    metadata:
      name: cpfsplugin.csi.alibabacloud.com
    spec:
      attachRequired: false
      podInfoOnMount: true
    ---
    # This YAML defines all API objects to create RBAC roles for csi node plugin.
    kind: DaemonSet
    apiVersion: apps/v1beta2
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

    执行以下命令检查运行状态。

    ```
    # kubectl get pod -nkube-system |grep cpfs
    csi-cpfsplugin-8t585                               2/2     Running   0          4h43m
    csi-cpfsplugin-9z5xj                               2/2     Running   0          4h43m
    csi-cpfsplugin-bdm22                               2/2     Running   0          4h43m
    csi-cpfsplugin-bjnlx                               2/2     Running   0          4h44m
    csi-cpfsplugin-nv7vg                               2/2     Running   0          4h43m
    csi-cpfsplugin-zc7z5                               2/2     Running   0          4h43m
    ```

2.  使用CPFS存储卷。

    1.  创建CPFS文件系统。具体步骤请参见[创建文件系统]()。

    2.  创建PV和PVC。

        拷贝以下模板，创建CPFS类型的PV和PVC。

        ```
        apiVersion: v1
        kind: PersistentVolume
        metadata:
          name: cpfs-csi-pv
          labels:
            alicloud-pvname: cpfs-pv
        spec:
          capacity:
            storage: 5Gi
          accessModes:
            - ReadWriteOnce
          persistentVolumeReclaimPolicy: Retain
          csi:
            driver: cpfsplugin.csi.alibabacloud.com
            volumeHandle: cpfs-csi-pv
            volumeAttributes:
              server: "xxxxxx@tcp:xxxxxx@tcp"
              fileSystem: "xxxxxx"
              subPath: "/k8s"
        ---
        kind: PersistentVolumeClaim
        apiVersion: v1
        metadata:
          name: cpfs-pvc
        spec:
          accessModes:
            - ReadWriteOnce
          resources:
            requests:
              storage: 5Gi
          selector:
            matchLabels:
              alicloud-pvname: cpfs-pv
        ```

    3.  创建应用。

        拷贝以下模板创建应用消费上述PVC。

        ```
        ---
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


