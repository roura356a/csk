---
keyword: [Mount NAS file systems to sandboxed containers, CSI-Plugin, Improve I/O performance]
---

# Mount a NAS file system to a sandboxed container through the CSI plug-in

You can use the Container Service Interface \(CSI\) plug-in to mount a NAS file system to a sandboxed container. This significantly improves I/O performance. This topic describes how to use the CSI plug-in to directly mount a NAS file system to a sandboxed container and provides an example to demonstrate the mounting process. A NAS file system can also be mounted to a container through the host or the 9PFS file system. This topic compares the I/O performance in the three mount modes.

-   [t1830927.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Create a managed ACK cluster that supports sandboxed containers.md)
-   [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to a cluster.md)

Sandboxed-Container allows containers to run on lightweight virtual machines. Sandboxed containers outperform containers in runC in terms of resource isolation and security. You can mount NAS file systems as volumes to sandboxed containers. In the community solution, a NAS file system is first mounted to a local directory of the host. Then, the local directory is shared to a container through 9PFS. However, 9PFS drastically degrades the I/O performance of containers. To improve I/O performance, Container Service for Kubernetes \(ACK\) allows you to mount NAS file systems directly to sandboxed containers through a CSI plug-in. This feature allows you to mount a NAS file system to a container after the container is started. Previously, you can only mount a NAS file system to the host before the container is started. This improves the I/O performance because 9PFS is no longer required for a container to access a NAS file system.

Starting from Sandboxed-Container v1.1.0, ACK enables this feature by default.

![Solutions for mounting NAS file systems to containers](../images/p95361.png "Comparison between the community solution and ACK solution")

## How the direct mount solution works

![How the direct mount solution works](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8665359951/p95364.png)

The following table describes how to use a CSI plug-in to mount a NAS file system to a sandboxed container.

|Step|Description|
|----|-----------|
|①|The kubelet requests the CSI plug-in to mount a NAS file system.|
|②|The CSI plug-in calls the QueryServer service to query whether a mounted volume that corresponds to the NAS file system exists. QueryServer is a local database that stores information about mounted volumes.|
|③|If no such mounted volume is found, information about the NAS file system, such as the mount point and mounted directory, is written to QueryServer.|
|④|When a pod is ready, the kubelet starts to create a container. The request to mount the NAS file system is eventually forwarded to Kata-Runtime.|
|⑤|Kata-Runtime calls the QueryServer service to query information about the NAS file system, including the mount point and mounted directory.|
|⑥|Kata-Runtime sends a request to Kata-Agent.|
|⑦|Kata-Agent starts a container and mounts the NAS file system to the container.|

## Example

The following example describes how to mount a NAS file system to a sandboxed container. In this example, an Apsara File Storage NAS instance is created and a YAML file template is used to create resource objects.

1.  Create an Apsara File Storage NAS instance. For more information, see [Create a file system]().

    **Note:** The NAS instance must be deployed in the same VPC as the cluster.

    Obtain the mount point URL of the NAS instance. In the following figure, file-system-id.region.nas.aliyuncs.com in the **Mount Command** column is the URL of the mount point.

    ![NAS](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8665359951/p95499.png)

    **Note:** file-system-id.region.nas.aliyuncs.com is the URL of the mount point. To obtain the mount point URL, log on to the [NAS console](https://nas.console.aliyun.com/), click the name of the target file system, and choose **Mounting Usage**.

2.  Use the following template to create resource objects:

    **Note:** Replace $\{nas-server-address\}in the template with the URL of the mount point.

    ```
    cat <<EOF | kubectl create -f -
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: nas-pvc-csi
      namespace: default
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 5Gi
      selector:
        matchLabels:
          alicloud-pvname: nas-pv-csi
    ---
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      labels:
        alicloud-pvname: nas-pv-csi
      name: nas-pv-csi
    spec:
      accessModes:
        - ReadWriteMany
      capacity:
        storage: 5Gi
      csi:
        driver: nasplugin.csi.alibabacloud.com
        volumeAttributes:
          options: noresvport,nolock
          path: /csi
          server: ${nas-server-address}
          vers: "3"
        volumeHandle: nas-pv-csi
      persistentVolumeReclaimPolicy: Retain
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: deploy-nas-csi
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: busybox
      template:
        metadata:
          labels:
            app: busybox
        spec:
          runtimeClassName: runv
          containers:
            - name: busybox
              image: registry.cn-hangzhou.aliyuncs.com/acs/busybox:v1.29.2
              command: 
              - tail
              - -f
              - /dev/null
              volumeMounts:
                - name: nas-pvc
                  mountPath: "/data"
          restartPolicy: Always
          volumes:
            - name: nas-pvc
              persistentVolumeClaim:
                claimName: nas-pvc-csi
    EOF
    ```

3.  Run the following commands to check the mount point type in the pod.

    ```
    kubectl get pods
    kubectl exec -it ${podid} sh
    mount | grep /data | grep nfs
    ```

    If the command output is not empty, the NAS file system is directly mounted to the container.


## I/O performance comparison

-   Random read operations per second

    |Mount mode|Result|
    |----------|------|
    |Host|read: IOPS=24.3k, BW=94.8MiB/s \(99.4MB/s\)\(5687MiB/60004msec\)|
    |CSI plug-in|read: IOPS=26.0k, BW=102MiB/s \(107MB/s\)\(6101MiB/60005msec\)|
    |9PFS|read: IOPS=780, BW=3120KiB/s \(3195kB/s\)\(183MiB/60001msec\)|

-   Random write operations per second

    |Mount mode|Result|
    |----------|------|
    |Host|write: IOPS=14.3k, BW=55.9MiB/s \(58.6MB/s\)\(3354MiB/60006msec\)|
    |CSI plug-in|write: IOPS=13.8k, BW=53.8MiB/s \(56.4MB/s\)\(3226MiB/60008msec\)|
    |9PFS|write: IOPS=356, BW=1428KiB/s \(1462kB/s\)\(83.7MiB/60001msec\)|

-   Random read throughput

    |Mount mode|Result|
    |----------|------|
    |Host|read: IOPS=331, BW=331MiB/s \(347MB/s\)\(29.9GiB/92439msec\)|
    |CSI plug-in|read: IOPS=251, BW=252MiB/s \(264MB/s\)\(29.9GiB/121561msec\)|
    |9PFS|read: IOPS=130, BW=130MiB/s \(136MB/s\)\(7861MiB/60422msec\)|

-   Random write throughput

    |Mount mode|Result|
    |----------|------|
    |Host|write: IOPS=507, BW=507MiB/s \(532MB/s\)\(29.9GiB/60372msec\)|
    |CSI plug-in|write: IOPS=508, BW=508MiB/s \(533MB/s\)\(29.9GiB/60255msec\)|
    |9PFS|write: IOPS=1, BW=1500KiB/s \(1536kB/s\)\(88.0MiB/60061msec\)|


According to the preceding data, the throughput and IOPS are almost the same when the NAS file system is mounted to the container and when the NAS file system is mounted to the host. The I/O performance is worst when the NAS file system is mounted through 9PFS.

