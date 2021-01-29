---
keyword: [cpfs-plugin, CPFS data storage]
---

# Static volumes

Cloud Paralleled File System \(CPFS\) is a parallel file system. CPFS stores data across multiple data nodes in a cluster and allows data to be simultaneously accessed by multiple clients. Therefore, CPFS can provide data storage services with high input/output operations per second \(IOPS\), high throughput, and low latency for large and high-performance computing clusters. This topic describes how to mount and use a CPFS volume in a Container Service for Kubernetes \(ACK\) cluster.

ACK allows you to mount CPFS file systems as static or dynamic volumes. To mount CPFS file systems as static volumes, you must manually create persistent volumes \(PVs\) and persistent volume claims \(PVCs\). This procedure can be time-consuming when large numbers of PVs and PVCs are required. In this case, you can mount CPFS file systems as dynamic volumes. In this topic, alibaba-cloud-csi-driver is used to mount a CPFS file system. For more information, see [alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver).

**Note:** ACK supports only CPFS 1.0. CPFS 2.0 is not supported.

## Procedure

1.  Deploy the cpfs-plugin component.

    To mount CPFS volumes, you must first perform the following steps to deploy cpfs-plugin in your ACK cluster:

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

    Run the following command to check the state of cpfs-plugin:

    ```
    # kubectl get pod -nkube-system |grep cpfs
    csi-cpfsplugin-8t585                               2/2     Running   0          4h43m
    csi-cpfsplugin-9z5xj                               2/2     Running   0          4h43m
    csi-cpfsplugin-bdm22                               2/2     Running   0          4h43m
    csi-cpfsplugin-bjnlx                               2/2     Running   0          4h44m
    csi-cpfsplugin-nv7vg                               2/2     Running   0          4h43m
    csi-cpfsplugin-zc7z5                               2/2     Running   0          4h43m
    ```

2.  Use a CPFS volume.

    1.  Create a CPFS file system. For more information, see [Create a file system]().

        Obtain the mount target of the NAS file system. Log on to the NAS console, find the created Apsara File Storage NAS. The URL \(file-system-id.region.nas.aliyuncs.com\) in the **Mount Command** column is the mount target, as shown in the following figure.

        ![NAS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8665359951/p95499.png)

        **Note:** file-system-id.region.nas.aliyuncs.com is the address of the mount target. You can obtain the address of the mount target in the NAS console. Log on to the [NAS console](https://nas.console.aliyun.com/), click the name of the NAS file system. In the left-side navigation pane, click **Mounting Usage**. You can find the address of the mount target in the Mount Command column.

    2.  Create a PV and a PVC.

        Use the following template to create a PV and a PVC for the CPFS file system:

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

    3.  Create a Deployment.

        Use the following template to create a Deployment that uses the preceding PVC:

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


