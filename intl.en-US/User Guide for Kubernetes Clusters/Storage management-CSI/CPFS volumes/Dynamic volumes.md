---
keyword: [CPFS data storage, cpfs-provisioner, cpfs-plugin]
---

# Dynamic volumes

Cloud Paralleled File System \(CPFS\) is a parallel file system. CPFS stores data across multiple data nodes in a cluster and allows data to be simultaneously accessed by multiple clients. Therefore, CPFS can provide data storage services with high input/output operations per second \(IOPS\), high throughput, and low latency for large and high-performance computing clusters. This topic describes how to mount and use a CPFS volume in a Container Service for Kubernetes \(ACK\) cluster.

ACK allows you to mount CPFS file systems as static or dynamic volumes. To mount CPFS file systems as static volumes, you must manually create persistent volumes \(PVs\) and persistent volume claims \(PVCs\). This procedure can be time-consuming when large numbers of PVs and PVCs are required. In this case, you can mount CPFS file systems as dynamic volumes. In this topic, alibaba-cloud-csi-driver is used to mount a CPFS volume. For more information, see [alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver).

**Note:** ACK supports only CPFS 1.0. CPFS 2.0 is not supported.

## Procedure

1.  Deploy the cpfs-plugin component.

    To mount CPFS file systems, you must first take the following steps to deploy components cpfs-provisioner and cpfs-plugin in your ACK cluster:

    -   Use the following template to deploy cpfs-provisioner:

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

    -   Deploy csi-plugin.

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

2.  Use a dynamic CPFS volume.

    1.  Use the following template to create a StorageClass:

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
        |volumeAs|The type of dynamic volume. Only the subpath type is supported. This type indicates that the volume is created from a subdirectory on the CPFS server.|
        |server|The address of the CPFS server.|
        |archiveOnDelete|Specifies how the CPFS subdirectory is handled when the corresponding PVC and PV are deleted.         -   If reclaimPolicy is set to Delete and archiveOnDelete is set to false, the subdirectory and its data are directly deleted. Exercise caution.
        -   If reclaimPolicy is set to Delete and archiveOnDelete is set to true, the subdirectory is renamed and archived.
        -   If reclaimPolicy is set to Retain, the subdirectory is retained. |

    2.  Use the following template to create a PVC:

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

    3.  Use the following template to create a Deployment:

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


