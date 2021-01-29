---
keyword: [CPFS, Cloud Paralleled File System, shared storage service]
---

# Use CPFS volumes in ACK clusters

You can use Cloud Paralleled File System \(CPFS\) volumes in Container Service for Kubernetes \(ACK\) clusters. This topic describes how to install the FlexVolume plug-in in ACK clusters and use CPFS volumes in pods.

-   A dedicated Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The ACK cluster can be accessed from the Internet through Secure Shell \(SSH\). For more information, see [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use SSH to connect to an ACK cluster.md).

**Note:** ACK supports only CPFS 1.0. CPFS 2.0 is not supported.

CPFS is a parallel file system. CPFS stores data across multiple data nodes in a cluster and allows data to be simultaneously accessed by multiple clients. Therefore, CPFS can provide data storage services with high input/output operations per second \(IOPS\), high throughput, and low latency for large-sized and high-performance computing clusters. CPFS is a shared storage service that meets the requirements on resource sharing and high performance. We recommend that you use ACK with CPFS in scenarios such as big data, artificial intelligence \(AI\), and genetic computing. For more information about CPFS, see [What is CPFS?]().

## Step 1: Install drivers

To use CPFS volumes in an ACK cluster, you must install the following drivers:

-   CPFS container driver: the flexvolume-cpfs plug-in that is compatible with all CentOS versions. You can deploy flexvolume-cpfs to install the CPFS container driver.
-   CPFS client driver: the driver of the CPFS client. It is similar to nfs-client. This driver is heavily reliant on the operating system kernel. You can install the CPFS client driver by using the following methods:

    -   Manually install the driver. For more information, see [Mount a file system]().
    -   When you deploy flexvolume-cpfs, the CPFS client driver is automatically installed. However, the driver does not support all operating system kernels.

        You can run the `uname -a` command on a node to query the kernel version of the operating system. You can install the CPFS client driver on the node that uses one of the following kernel versions:

        ```
        3.10.0-957.5.1
        3.10.0-957.21.3
        3.10.0-1062.9.1
        ```

    **Note:**

    -   FlexVolume allows you to only install the CPFS client driver \(cpfs-client\). After you install the driver on a node, you cannot reinstall and upgrade the driver.
    -   When you upgrade FlexVolume, only flexvolume-cpfs \(CPFS container driver\) is upgraded. cpfs-client \(CPFS client driver\) is not upgraded.
    -   When you install flexvolume-cpfs on nodes where cpfs-client and lustre are deployed, cpfs-client of the latest version is not automatically installed.
    -   You can only manually upgrade cpfs-client. For more information, see [Mount a file system]().

1.  Deploy a YAML template on a node.

    1.  [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md) from a client.

    2.  Create a flexvolume-cpfs.yaml file.

    3.  Copy the following content to the file:

        ```
        apiVersion: apps/v1
        kind: DaemonSet
        metadata:
          name: flexvolume-cpfs
          namespace: kube-system
          labels:
            k8s-volume: flexvolume-cpfs
        spec:
          selector:
            matchLabels:
              name: flexvolume-cpfs
          template:
            metadata:
              labels:
                name: flexvolume-cpfs
            spec:
              hostPID: true
              hostNetwork: true
              tolerations:
              - operator: "Exists"
              priorityClassName: system-node-critical
              affinity:
                nodeAffinity:
                  requiredDuringSchedulingIgnoredDuringExecution:
                    nodeSelectorTerms:
                    - matchExpressions:
                      - key: type
                        operator: NotIn
                        values:
                        - virtual-kubelet
              containers:
              - name: flexvolume-cpfs
                image: registry.cn-hangzhou.aliyuncs.com/acs/flexvolume:v1.14.8.96-0d85fd1-aliyun
                imagePullPolicy: Always
                securityContext:
                  privileged: true
                env:
                - name: ACS_CPFS
                  value: "true"
                - name: FIX_ISSUES
                  value: "false"
                livenessProbe:
                  exec:
                    command:
                    - sh
                    - -c
                    - ls /acs/flexvolume
                  failureThreshold: 8
                  initialDelaySeconds: 15
                  periodSeconds: 60
                  successThreshold: 1
                  timeoutSeconds: 15
                volumeMounts:
                - name: usrdir
                  mountPath: /host/usr/
                - name: etcdir
                  mountPath: /host/etc/
                - name: logdir
                  mountPath: /var/log/alicloud/
                - mountPath: /var/lib/kubelet
                  mountPropagation: Bidirectional
                  name: kubeletdir
              volumes:
              - name: usrdir
                hostPath:
                  path: /usr/
              - name: etcdir
                hostPath:
                  path: /etc/
              - name: logdir
                hostPath:
                  path: /var/log/alicloud/
              - hostPath:
                  path: /var/lib/kubelet
                  type: Directory
                name: kubeletdir
          updateStrategy:
            rollingUpdate:
              maxUnavailable: 10%
            type: RollingUpdate
        ```

    4.  Run the following command to deploy the YAML file on the node:

        ```
        `kubectl create -f flexvolume-cpfs.yaml`
        ```

2.  Check the deployment result.

    1.  Run the following command to verify that the volume plug-ins are deployed:

        ```
        kubectl get pod -nkube-system | grep flex
        ```

        The following output is returned:

        ```
        flexvolume-97psk                                  1/1     Running   0          27m
        flexvolume-cpfs-dgxfq                             1/1     Running   0          98s
        flexvolume-cpfs-qpbcb                             1/1     Running   0          98s
        flexvolume-cpfs-vlrf9                             1/1     Running   0          98s
        flexvolume-cpfs-wklls                             1/1     Running   0          98s
        flexvolume-cpfs-xtl9b                             1/1     Running   0          98s
        flexvolume-j8zjr                                  1/1     Running   0          27m
        flexvolume-pcg4l                                  1/1     Running   0          27m
        flexvolume-tjxxn                                  1/1     Running   0          27m
        flexvolume-x7ljw                                  1/1     Running   0          27m
        ```

        **Note:** The flexvolume-cpfs plug-in is deployed on pods whose names are prefixed with flexvolume-cpfs. Pods whose names do not contain cpfs are deployed with the FlexVolume plug-in. Alibaba Cloud disks, Apsara File Storage NAS \(NAS\) file systems, and Object Storage Service \(OSS\) buckets can be mounted to these pods. Both types of plug-ins can be deployed on the same node.

    2.  Run the following command to check whether cpfs-client is installed:

        ```
        rpm -qa | grep cpfs
        ```

        The following output is returned:

        ```
        kmod-cpfs-client-2.10.8-202.el7.x86_64
        cpfs-client-2.10.8-202.el7.x86_64
        ```

    3.  Run the following command to check whether mount.lustre is installed:

        ```
        which mount.lustre
        ```

        The following output is returned:

        ```
        /usr/sbin/mount.lustre
        ```


## Step 2: Use a CPFS volume

To use a CPFS volume in an ACK cluster, you must first create a CPFS file system and a mount target in the CPFS console. For more information, see [Create a file system]().

**Note:** When you create a CPFS mount target, select a virtual private cloud \(VPC\). The ACK cluster and the mount target must be deployed in the same VPC.

In the following example, the mount target is *cpfs-\*-alup.cn-shenzhen.cpfs.nas.aliyuncs.com@tcp:cpfs--ws5v.cn-shenzhen.cpfs.nas.aliyuncs.com@tcp* and the file system ID is *0237ef41*.

1.  Create a persistent volume \(PV\).

    1.  Create a pv-cpfs.yaml file.

    2.  Copy the following content to the file:

        ```
        apiVersion: v1
        kind: PersistentVolume
        metadata:
          name: pv-cpfs
          labels:
            alicloud-pvname: pv-cpfs
        spec:
          capacity:
            storage: 5Gi
          accessModes:
            - ReadWriteMany
          flexVolume:
            driver: "alicloud/cpfs"
            options:
              server: "cpfs-****-alup.cn-shenzhen.cpfs.nas.aliyuncs.com@tcp:cpfs-***-ws5v.cn-shenzhen.cpfs.nas.aliyuncs.com@tcp"
              fileSystem: "0237ef41"
              subPath: "/k8s"
              options: "ro"
        ```

        |Parameter|Description|
        |---------|-----------|
        |server|Set the value to the mount target of the CPFS file system.|
        |fileSystem|Set the value to the ID of the CPFS file system.|
        |subPath|Set the value to a subdirectory of the CPFS root.|
        |options|Other mount options. This parameter is optional.|

    3.  Run the following command to create a PV:

        ```
        kubectl create -f pv-cpfs.yaml
        ```

2.  Create a persistent volume claim \(PVC\).

    1.  Create a pvc-cpfs file and copy the following content to the file:

        ```
        kind: PersistentVolumeClaim
        apiVersion: v1
        metadata:
          name: pvc-cpfs
        spec:
          accessModes:
            - ReadWriteMany
          resources:
            requests:
              storage: 5Gi
          selector:
            matchLabels:
              alicloud-pvname: pv-cpfs
        ```

    2.  Run the `kubectl create -f pvc-cpfs` command to create a PVC.

3.  Create a Deployment.

    1.  Create a nas-cpfs file and copy the following content to the file:

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: nas-cpfs
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
                  - name: pvc-cpfs
                    mountPath: "/data"
              volumes:
                - name: pvc-cpfs
                  persistentVolumeClaim:
                    claimName: pvc-cpfs
        ```

    2.  Run the `kubectl create -f nas-cpfs` command to create a Deployment.


Run the following command to query the mounting states of the pods that run on the node:

```
kubectl get pod
```

The following output is returned:

```
NAME                        READY   STATUS    RESTARTS   AGE
nas-cpfs-79964997f5-kzrtp   1/1     Running   0          45s
```

Run the following command to query the directories that are mounted to pods on the node:

```
kubectl exec -ti nas-cpfs-79964997f5-kzrtp sh
mount | grep k8s
```

The following output is returned:

```
192.168.1.12@tcp:192.168.1.10@tcp:/0237ef41/k8s on /data type lustre (ro,lazystatfs)
```

Run the following command to query the mounted directories on the node:

```
mount | grep cpfs
```

The following output is returned:

```
192.168.1.12@tcp:192.168.1.10@tcp:/0237ef41/k8s on /var/lib/kubelet/pods/c4684de2-26ce-11ea-abbd-00163e12e203/volumes/alicloud~cpfs/pv-cpfs type lustre (ro,lazystatfs)
```

**Related topics**  


[Use the CSI-CPFS plug-in](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/blob/master/docs/cpfs.md)

