---
keyword: [Object Storage Service, OSS, persistent storage]
---

# Use OSS volumes for persistent storage

When a node that hosts running containers fails, stateful applications may lose the business data stored in the containers. This issue can be resolved by using persistent storage. This topic describes how to use an Object Storage Service \(OSS\) volume to persist data.

## Background information

OSS is a secure, cost-effective, and highly reliable cloud storage service provided by Alibaba Cloud. You can mount an OSS volume on multiple pods of a Container Service for Kubernetes \(ACK\) cluster.

Scenarios

-   Average requirements on disk I/O
-   Sharing of data, including configuration files, images, and small video files

Procedure

1.  Create an OSS bucket.
2.  Obtain an **AccessKey ID** and **AccessKey secret**.
3.  Create a persistent volume \(PV\) and a persistent volume claim \(PVC\).

## Prerequisites

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).
-   An OSS bucket is created in the OSS console. For more information, see [Create buckets](/intl.en-US/Quick Start/OSS console/Create buckets.md).

## Precautions

-   kubelet and the OSSFS driver may be restarted when the ACK cluster is upgraded. As a result, the mounted OSS directory becomes unavailable. In this case, you must recreate the pods on which the OSS volume is mounted. You can add health check settings in the YAML file to restart the pods and remount the OSS volume when the OSS directory becomes unavailable.
-   If your ACK cluster is of the latest Kubernetes version, the preceding issue is fixed.

## Create a PV

1.  Create a file named pv-oss.yaml.

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
          bucket: "docker"                        //Replace the value with the bucket name. 
          url: "oss-cn-hangzhou.aliyuncs.com"     //Replace the value with the endpoint of the OSS bucket. 
          akId: "***"                             //Replace the value with the AccessKey ID. 
          akSecret: "***"                         //Replace the value with the AccessKey secret. 
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"   //Replace the value with custom parameter values. 
    ```

    Parameter description

    -   `alicloud-pvname`: the name of the PV. This value must be used in the `selector` field of the PVC that is associated with the PV.
    -   `bucket`: the name of the OSS bucket. Only OSS buckets can be mounted to the ACK cluster. You cannot mount the subdirectories or files in an OSS bucket to the ACK cluster.
    -   `url`: the endpoint of the OSS bucket. For more information, see [Regions and endpoints](/intl.en-US/Developer Guide/Endpoint/Regions and endpoints.md). To obtain the endpoint, log on to the OSS console. In the left-side navigation pane, find the OSS bucket that you want to manage. On the **Overview** page, find the Domain Names section and view the endpoint of the OSS bucket in the **Endpoint** column.
    -   `akId`: the AccessKey ID. Log on to the ACK console, move the pointer over the ![user](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1571474161/p161100.png) icon in the upper-right corner of the page and select **AccessKey Management** from the shortcut menu. On the page that appears, create an **AccessKey ID** and an **AccessKey secret**.
    -   `akSecret`: the AccessKey secret. To obtain the AccessKey secret, perform the steps described in `akId`.
    -   `otherOpts`: the custom parameters that are used to mount the OSS bucket. The parameters must be in the following format: `-o *** -o ***`.
2.  Run the following command to create a PV:

    ```
    kubectl create -f pv-oss.yaml
    ```


**Expected result**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.
4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**. Verify that the newly created PV is displayed.

## Create a PVC

Create a PVC of the OSS type. Set the `selector` parameter to configure how to select the PV to which the PVC is bound. Set the `storageClassName` parameter to bind the PVC with the PV of the OSS type.

1.  Create a file named pvc-oss.yaml.

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


**Expected result**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.
4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volume Claims**. Verify that the newly created PVC is displayed.

## Create an application

1.  Create a file named oss-static.yaml.

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

    **Note:** For more information about how to set `livenessProbe` to configure health checks, see [Mount OSS volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/OSS volumes/Mount OSS volumes.md).

2.  Run the following command to deploy an application:

    ```
    kubectl create -f oss-static.yaml d
    ```


**Expected result**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Applications** in the **Actions** column.
4.  In the left-side navigation pane of the cluster details page, choose **Workloads** \> **Deployments**. Verify that the newly created application is displayed.

## Verify data persistence

1.  Run the following command to query the pods that run the application:

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    oss-static-66fbb85b67-dqbl2      1/1     Running   0          1h
    ```

2.  Run the following command to query the files in the /data path:

    ```
    kubectl exec oss-static-66fbb85b67-dqbl2 ls /data | grep tmpfile
    ```

    **Note:** The output indicates that no file exists in the /data path.

3.  Run the following command to create a file named tmpfile in the /data path:

    ```
    kubectl exec oss-static-66fbb85b67-dqbl2 touch /data/tmpfile
    ```

4.  Run the following command to query the files in the /data path:

    ```
    kubectl exec oss-static-66fbb85b67-dqbl2 ls /data | grep tmpfile
    ```

    Expected output:

    ```
    tmpfile
    ```

5.  Run the following command to delete the pod named oss-static-66fbb85b67-dqbl2:

    ```
    kubectl delete pod oss-static-66fbb85b67-dqbl2
    ```

    Expected output:

    ```
    pod "oss-static-66fbb85b67-dqbl2" deleted
    ```

6.  Open another kubectl command-line interface \(CLI\) and run the following command to view how the pod is deleted and recreated:

    ```
    kubectl get pod -w -l app=nginx
    ```

    Expected output:

    ```
    NAME                          READY   STATUS            RESTARTS   AGE
    oss-static-66fbb85b67-dqbl2   1/1     Running           0          78m
    oss-static-66fbb85b67-dqbl2   1/1     Terminating       0          78m
    oss-static-66fbb85b67-zlvmw   0/1     Pending           0          <invalid>
    oss-static-66fbb85b67-zlvmw   0/1     Pending           0          <invalid>
    oss-static-66fbb85b67-zlvmw   0/1     ContainerCreating 0          <invalid>
    oss-static-66fbb85b67-dqbl2   0/1     Terminating       0          78m
    oss-static-66fbb85b67-dqbl2   0/1     Terminating       0          78m
    oss-static-66fbb85b67-dqbl2   0/1     Terminating       0          78m
    oss-static-66fbb85b67-zlvmw   1/1     Running           0          <invalid>
    ```

7.  Run the following command to query the recreated pod:

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    oss-static-66fbb85b67-zlvmw      1/1     Running   0          40s
    ```

8.  Run the following command to verify that the tmpfile file exists in the /data path. This indicates that data is persisted to the OSS volume.

    ```
    kubectl exec oss-static-66fbb85b67-zlvmw ls /data | grep tmpfile
    ```

    Expected output:

    ```
    tmpfile
    ```


