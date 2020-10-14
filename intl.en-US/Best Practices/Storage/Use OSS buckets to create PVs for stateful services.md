# Use OSS buckets to create PVs for stateful services

This topic describes the common scenarios of Object Storage Service \(OSS\) buckets and how to use buckets to create persistent volumes \(PVs\) for stateful services.

## Background

OSS is a secure, cost-effective, and highly reliable cloud storage service provided by Alibaba Cloud. You can use OSS to store a large amount of data in the cloud. You can mount a bucket to multiple pods.

When to use OSS buckets:

-   Low disk I/O requirements
-   Data sharing, including configuration files, images, and small video files

How to use OSS buckets:

1.  Create a bucket.
2.  Obtain the **AccessKey ID** and the **AccessKey Secret**.
3.  Create a PV and a persistent volume claim \(PVC\).

## Prerequisites

-   A Container Service for Kubernetes \(ACK\) cluster is created. For more information, see [t16639.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Proprietary Edition.md).
-   The cluster is connected through kubectl. For more information, see [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to a cluster.md).
-   A bucket is created in the OSS console. For more information, see [Create buckets](/intl.en-US/Quick Start/Create buckets.md).

## Considerations

-   The upgrade of a cluster causes the restart of kubelet and the OSSFS driver. As a result, the OSS directory becomes unavailable. The pods that use the OSS bucket must be created again. We recommend that you add a rule in the YAML file of your application to restart the pods and remount the OSS bucket when the OSS directory becomes unavailable.
-   If you use a cluster of the latest version, the preceding issue does not affect you.

## Create a PV

1.  Add the following content to the pv-oss.yaml file.

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: pv-oss
      labels:
        alicloud-pvname: pv-oss
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteMany
      storageClassName: oss
      flexVolume:
        driver: "alicloud/oss"
        options:
          bucket: "docker"                        ////Replace the value with the bucket name.
          url: "oss-cn-hangzhou.aliyuncs.com"     ////Replace the value with the endpoint of the OSS bucket.
          akId: "***"                             ////Replace the value with the AccessKey ID.
          akSecret: "***"                         ////Replace the value with the AccessKey secret.
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"   ////Replace the value with the custom parameter values.
    ```

    Parameter description

    -   `alicloud-pvname`: the name of the PV. This parameter value must be used in the `selector` field of the PVC that is associated with the PV.
    -   `bucket`: the name of the bucket. Only buckets can be mounted to a cluster. You cannot mount the subdirectories or files in a bucket to a cluster.
    -   `URL`: the endpoint of the OSS bucket. For more information, see [Regions and endpoints](/intl.en-US/Developer Guide/Endpoint/Regions and endpoints.md). To obtain the endpoint, log on to the OSS console. In the left-side navigation pane, select the bucket that you want to view. In the Domain Names section, view **Endpoint**.
    -   `akId`: the AccessKey ID. To obtain the **AccessKey ID** and the **AccessKey secret**, click ![user](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5845359951/p34867.png) in the upper-right corner of the ACK console. If the current user is an Alibaba Cloud account, select **accesskeys**. If the current user is a RAM user, select **AccessKey Management**.
    -   `akSecret`: the AccessKey secret. To obtain the AccessKey secret, follow the steps described in `akId`.
    -   `otherOpts`: the custom parameters for mounting the OSS bucket. Set the value in the format of `-o *** -o ***`. For more information, see [t4872.md\#](/intl.en-US/Tools/ossfs/FAQ.md).
2.  Run the following command to create a PV:

    ```
    kubectl create -f pv-oss.yaml
    ```


In the left-side navigation pane of the ACK console, choose **Clusters** \> **Persistent Volumes**. On the Persistent Volumes tab, select the cluster where the PV is created. You can view the newly created PV.

![Persistent Volumes](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5845359951/p34869.png)

## Create a PVC

Create a PVC for OSS. Use the `selector` field to filter for the created PV. In this case, you can bind the PVC to the PV. Specify the `storageClassName` parameter to bind the PVC to the PV of the OSS type.

1.  Add the following content to the pvc-oss.yaml file:

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: pvc-oss
    spec:
      accessModes:
        - ReadWriteMany
      storageClassName: oss
      resources:
        requests:
          storage: 5Gi
      selector:
        matchLabels:
          alicloud-pvname: pv-oss
    ```

2.  Run the following command to create a PVC:

    ```
    kubectl create -f pvc-oss.yaml
    ```


In the left-side navigation pane, choose **Applications** \> **Persistent Volume Claims**. On the Persistent Volume Claims tab, select the cluster and namespace where the PVC is created. You can view the newly created PVC.

![Persistent Volume Claims](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5845359951/p34871.png)

## Create an application

1.  Add the following content to the oss-static.yaml file:

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: oss-static
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
              - name: pvc-oss
                mountPath: "/data"
              - name: pvc-oss
                mountPath: "/data1"
            livenessProbe:
              exec:
                command:
                - sh
                - -c
                - cd /data
              initialDelaySeconds: 30
              periodSeconds: 30
          volumes:
            - name: pvc-oss
              persistentVolumeClaim:
                claimName: pvc-oss
    ```

    **Note:** For more information about how to configure `livenessProbe`, see [Use OSS volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/OSS volumes/Use OSS volumes.md).

2.  Run the following command to create an application:

    ```
    kubectl create -f oss-static.yaml d
    ```


In the left-side navigation pane, choose **Applications** \> **Deployments**. On the Deployments page, select the desired cluster and namespace. You can view the newly created application.

![Deployments](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6845359951/p34888.png)

## Use OSS buckets for persistent storage

1.  Run the following command to query the pod that runs the application:

    ```
    kubectl get pod
    ```

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    oss-static-66fbb85b67-dqbl2      1/1     Running   0          1h
    ```

2.  Run the following command to query the files in the /data path:

    ```
    kubectl exec oss-static-66fbb85b67-dqbl2 ls /data | grep tmpfile
    ```

    **Note:** The /data path is empty.

3.  Run the following command to create file tmpfile in the /data path:

    ```
    kubectl exec oss-static-66fbb85b67-dqbl2 touch /data/tmpfile
    ```

4.  Run the following command to view the files in the /data path:

    ```
    kubectl exec oss-static-66fbb85b67-dqbl2 ls /data | grep tmpfile
    ```

    ```
    tmpfile
    ```

5.  Run the following command to delete pod oss-static-66fbb85b67-dqbl2:

    ```
    kubectl delete pod oss-static-66fbb85b67-dqbl2
    ```

    ```
    pod "oss-static-66fbb85b67-dqbl2" deleted
    ```

6.  Open another kubectl CLI and run the following command to query how the pod is deleted and a new pod is created:

    ```
    kubectl get pod -w -l app=nginx
    ```

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    oss-static-66fbb85b67-dqbl2      1/1     Running   0          78m
    oss-static-66fbb85b67-dqbl2   1/1   Terminating   0     78m
    oss-static-66fbb85b67-zlvmw   0/1   Pending   0     <invalid>
    oss-static-66fbb85b67-zlvmw   0/1   Pending   0     <invalid>
    oss-static-66fbb85b67-zlvmw   0/1   ContainerCreating   0     <invalid>
    oss-static-66fbb85b67-dqbl2   0/1   Terminating   0     78m
    oss-static-66fbb85b67-dqbl2   0/1   Terminating   0     78m
    oss-static-66fbb85b67-dqbl2   0/1   Terminating   0     78m
    oss-static-66fbb85b67-zlvmw   1/1   Running   0     <invalid>
    ```

7.  Run the following command to query the new pod:

    ```
    kubectl get pod
    ```

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    oss-static-66fbb85b67-zlvmw      1/1     Running   0          40s
    ```

8.  Run the following command to verify that file tmpfile exists in the /data path. This indicates that data in the OSS bucket is permanently stored.

    ```
    kubectl exec oss-static-66fbb85b67-zlvmw ls /data | grep tmpfile
    ```

    ```
    tmpfile
    ```


