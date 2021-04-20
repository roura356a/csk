# Mount a disk to a sandboxed container

You can mount a disk to a sandboxed container to significantly improve I/O performance. This topic describes how to mount a disk to a sandboxed container.

-   [Create a managed Kubernetes cluster that runs sandboxed containers](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Create a security sandbox cluster/Create a managed Kubernetes cluster that runs sandboxed containers.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

virtio-fs is a shared file system. The Sandboxed-Container runtime provided by Container Service for Kubernetes supports virtio-fs. It allows you to add volumes, Secrets, and ConfigMaps to the guest operating system of a virtual machine \(VM\). This allows you to mount a disk as a volume to a sandboxed container. This method mounts the disk to the host. Applications in the container can write data to and read data from the disks only through virtio-fs. This may cause performance degradation.

Sandboxed containers allow you to directly mount disks. This method first unmounts disk mount targets from the host. Then, the disk is mounted to the guest operating system before the system creates a bind mount for the disk. This way, applications in the container can directly write data to and read data from the disk without performance degradation.

![Mount a disk to a sandboxed container](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4549988161/p186229.png)

## How a disk is mounted to a sandboxed container

![How a disk is mounted to a sandboxed container](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4549988161/p186232.png)

A disk is mounted to a sandboxed container in the following process.

|Step|Description|
|----|-----------|
|①|The kubelet requests the CSI plug-in to mount a disk.|
|②|The CSI plug-in formats the disk and mounts the disk to the host.|
|③|The kubelet requests Kangaroo-Runtime to create a pod.|
|④|Kangaroo-Runtime parses the disk unmounting information and unmounts the disk from the host.|
|⑤|Kangaroo-Runtime requests the agent to create a pod.|
|⑥|The agent mounts the disk to the guest operating system.|
|⑦|The agent creates a bind mount for the disk that is mounted to the guest operating system.|

## Examples

The following example shows how to mount a disk to a sandboxed container. In this example, a YAML file template is used to create resource objects.

1.  Use the following template to create resource objects:

    ```
    cat <<EOF | kubectl create -f -
    allowVolumeExpansion: true
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: alicloud-disk-ssd
    parameters:
      type: cloud_ssd
    provisioner: diskplugin.csi.alibabacloud.com
    reclaimPolicy: Delete
    ---
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: disk-pvc-01
      namespace: default
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 25Gi
      storageClassName: alicloud-disk-ssd
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: busybox
      name: busybox
      namespace: default
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: busybox
      template:
        metadata:
          labels:
            app: busybox
          annotations:
            storage.alibabacloud.com/enable_ebs_passthrough: "true"
        spec:
          containers:
            - name: busybox
              image: registry.cn-hangzhou.aliyuncs.com/acs/busybox:v1.29.2
              command:
              - tail
              - -f
              - /dev/null
              volumeMounts:
                - mountPath: "/data"
                  name: disk-pvc
          dnsPolicy: ClusterFirst
          restartPolicy: Always
          runtimeClassName: runv
          volumes:
            - name: disk-pvc
              persistentVolumeClaim:
                claimName: disk-pvc-01
    EOF
    ```

    **Note:**

    By default, pods cannot communicate with disks. You must add an annotation in the template to enable the pod to communicate with disks.

    ```
    annotations:
            storage.alibabacloud.com/enable_ebs_passthrough: "true"
    ```

2.  Run the following commands to check the type of file system that is mounted to the pod:

    ```
    kubectl get pods
    ```

    ```
    kubectl exec -it ${podid} sh
    ```

    ```
    mount | grep /data | grep -vi virtio_fs
    ```

    If the type of file system is not virtio-fs, it indicates that the disk is mounted to the container.


