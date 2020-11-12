---
keyword: [CPFS data storage, cpfs-provisioner, cpfs-plugin]
---

# Dynamic volumes

Cloud Paralleled File System \(CPFS\) is a parallel file system. CPFS stores data across multiple data nodes in a cluster and allows data to be simultaneously accessed by multiple clients. Therefore, it can provide data storage services with high IOPS, high throughput, and low latency for large-scale, high-performance computing clusters. This topic describes how to mount and use a CPFS volume.

Container Service for Kubernetes allows you to mount CPFS volumes in static or dynamic manner. To mount a static volume, you need to manually create a PV and a PVC. This procedure can be time consuming when large numbers of PVs and PVCs are required. In this case, you can dynamically mount CPFS volumes. This topic demonstrates how to use alibaba-cloud-csi-driver to mount CPFS volumes. For more information, see [alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver).

1.  Deploy the cpfs-plugin component.

    To mount CPFS volumes, take the following steps to deploy components cpfs-provisioner and cpfs-plugin in the target cluster first.

    -   Deploy cpfs-provisioner with the following template:

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
                  image: registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.4.0-aliyun
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

    -   Deploy csi-plugin.

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

2.  Use a dynamic CPFS volume.

    1.  Create a StorageClass with the following template:

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

        |Parameter|Description|
        |---------|-----------|
        |volumeAs|The type of the dynamic volume. Currently, only the subpath type is supported. This type indicates that the volume is created from a sub-directory on the CPFS server.|
        |server|The address of the CPFS server.|
        |archiveOnDelete|How to handle the CPFS sub-directory when the corresponding PVC and PV are deleted.         -   If reclaimPolicy is set to Delete and archiveOnDelete is set to false, the sub-directory and its data are directly deleted. Exercise caution.
        -   If reclaimPolicy is set to Delete and archiveOnDelete is set to true, the sub-directory is renamed and archived.
        -   If reclaimPolicy is set to Retain, the sub-directory is retained. |

    2.  Create a PVC with the following template:

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

        **Note:** Specify storageClassName in the PVC definition.

    3.  Create an application with the following template:

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


