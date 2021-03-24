# Use OSS buckets to create PVs for stateful services

This topic describes the common scenarios of Object Storage Service \(OSS\) buckets and how to use OSS buckets to create persistent volumes \(PVs\) for stateful services.

## Background information

OSS is a secure, cost-effective, and highly reliable cloud storage service provided by Alibaba Cloud. You can use OSS to store a large amount of data in the cloud. You can mount an OSS bucket to multiple pods of a Container Service for Kubernetes \(ACK\) cluster.

Scenarios of OSS buckets:

-   Average requirements on disk I/O
-   Sharing of data, including configuration files, images, and small video files

Use of OSS buckets:

1.  Create a bucket.
2.  Obtain the **AccessKey ID** and the **AccessKey secret**.
3.  Create a PV and a persistent volume claim \(PVC\).

## Prerequisites

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).
-   A bucket is created in the OSS console. For more information, see [Create buckets](/intl.en-US/Quick Start/OSS console/Create buckets.md).

## Considerations

-   The kubelet and OSSFS driver may be restarted when the ACK cluster is upgraded. Therefore, the OSS directory becomes unavailable. The pods that use the OSS bucket must be created again. We recommend that you add a rule in the YAML file of your application to restart the pods and remount the OSS bucket when the OSS directory becomes unavailable.
-   If you use an ACK cluster of the latest version, the preceding issue can be avoided.

## Create a PV

1.  Create a pv-oss.yaml file and copy the following content to the file:

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
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"   //Replace the value with the custom parameter values.
    ```

    Parameter description

    -   `alicloud-pvname`: the name of the PV. This parameter value must be used in the `selector` field of the PVC that is associated with the PV.
    -   `bucket`: the name of the bucket. Only buckets can be mounted to the ACK cluster. You cannot mount the subdirectories or files in a bucket to the ACK cluster.
    -   `url`: the endpoint of the OSS bucket. For more information, see [Regions and endpoints](/intl.en-US/Developer Guide/Endpoint/Regions and endpoints.md). To obtain the endpoint, log on to the OSS console. In the left-side navigation pane, click the name of the bucket that you want to view. The **Overview** page appears. In the Domain Names section, view the endpoints of the bucket in the **Endpoint** column.
    -   `akId`: the AccessKey ID. Log on to the ACK console, move the pointer over the icon ![user](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1571474161/p161100.png) in the upper-right corner of the page and select **AccessKey Management** from the shortcut menu. On the page that appears, create the **AccessKey ID** and **AccessKey secret**.
    -   `akSecret`: the AccessKey secret. To obtain the AccessKey secret, perform the steps described in `akId`.
    -   `otherOpts`: the custom parameters that are used to mount the OSS bucket. Set the value in the format of `-o *** -o ***`. For more information, see [t4872.md\#](/intl.en-US/Tools/ossfs/FAQ.md).
2.  Run the following command to create a PV:

    ```
    kubectl create -f pv-oss.yaml
    ```


Verify the result

In the left-side navigation pane of the ACK console, choose **Clusters** \> **Persistent Volumes**. On the Persistent Volumes tab, select the cluster where the PV is created. You can view the newly created PV.

![Persistent Volumes](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5845359951/p34869.png)

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  Log on to the ACK console. In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster where the PV is created and click **Details** in the **Actions** column of the cluster.
4.  In the left-side pane, click **Persistent Volumes**.
5.  Click the **Persistent Volumes** tab to view the newly created PV.

## Create a PVC

Create a PVC for OSS. Set the `selector` field to search for the created PV. In this case, you can associate the PVC with the PV. Set the `storageClassName` parameter to associate the PVC with the PV of the OSS type.

1.  Create a pvc-oss.yaml file and copy the following content to the file:

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


In the left-side navigation pane of the ACK console, choose **Applications** \> **Persistent Volume Claims**. On the Persistent Volume Claims tab, select the cluster and namespace where the PVC is created. You can view the newly created PVC.

![Persistent Volume Claims](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5845359951/p34871.png)

Verify the result

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster where the PVC is created, and click the cluster name or **Details** in the **Actions** column of the cluster.
4.  The Cluster Information page appears. In the left-side navigation pane, click **Persistent Volumes**.
5.  Click the **Persistent Volume Claims** tab to view the newly created PVC.

## Deploy an application

1.  Create an oss-static.yaml file and copy the following content to the file:

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

    **Note:** For more information about how to set `livenessProbe`, see [Use an OSS volume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/OSS volumes/Use an OSS volume.md).

2.  Run the following command to deploy an application:

    ```
    kubectl create -f oss-static.yaml d
    ```


In the left-side navigation pane, choose **Applications** \> **Deployments**. On the Deployments page, select the cluster and namespace that you want to manage. You can view the newly created deployment.

![Deployments](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6845359951/p34888.png)

Verify the result

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster where the deployment is created, and click the cluster name or **Applications** in the **Actions** column of the cluster.
4.  The Cluster Information page appears. In the left-side navigation pane, click **Workload**.
5.  On the page that appears, click the **Deployments** tab to view the newly created deployment.

## Use OSS buckets for persistent storage

1.  Run the following command to query the pod that runs the application:

    ```
    kubectl get pod
    ```

    The following output is returned:

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    oss-static-66fbb85b67-dqbl2      1/1     Running   0          1h
    ```

2.  Run the following command to view the files in the /data path of each pod:

    ```
    kubectl exec oss-static-66fbb85b67-dqbl2 ls /data | grep tmpfile
    ```

    **Note:** The /data path is empty.

3.  Run the following command to create the file tmpfile in the /data path:

    ```
    kubectl exec oss-static-66fbb85b67-dqbl2 touch /data/tmpfile
    ```

4.  Run the following command to view files in the /data path of each pod:

    ```
    kubectl exec oss-static-66fbb85b67-dqbl2 ls /data | grep tmpfile
    ```

    The following output is returned:

    ```
    tmpfile
    ```

5.  Run the following command to delete the pod oss-static-66fbb85b67-dqbl2:

    ```
    kubectl delete pod oss-static-66fbb85b67-dqbl2
    ```

    The following output is returned:

    ```
    pod "oss-static-66fbb85b67-dqbl2" deleted
    ```

6.  Open another kubectl command-line interface \(CLI\) and run the following command to query how the pod is deleted and a new pod is created:

    ```
    kubectl get pod -w -l app=nginx
    ```

    The following output is returned:

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

7.  Run the following command to view the new pod:

    ```
    kubectl get pod
    ```

    The following output is returned:

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    oss-static-66fbb85b67-zlvmw      1/1     Running   0          40s
    ```

8.  Run the following command to verify that the file tmpfile exists in the /data path. This indicates that data in the OSS bucket is permanently stored.

    ```
    kubectl exec oss-static-66fbb85b67-zlvmw ls /data | grep tmpfile
    ```

    The following output is returned:

    ```
    tmpfile
    ```


