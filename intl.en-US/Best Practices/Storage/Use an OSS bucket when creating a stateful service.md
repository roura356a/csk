# Use an OSS bucket when creating a stateful service {#concept_x2l_5qr_2gb .concept}

This topic describes typical scenarios in which an Object Storage Service \(OSS\) bucket is needed for creating a stateful service, and the procedure for how to use the bucket.

## Scenarios and method {#section_jxw_sxr_2gb .section}

Alibaba Cloud OSS provides massive, secure, low-cost, and highly reliable cloud storage services. An OSS bucket can be mounted to multiple pods.

**Scenarios:**

-   Disk I/O performance requirements are low.
-   Shared services such as files, figures, and short videos are to be configured.

**Method:**

1.  Manually create a bucket.
2.  Obtain the **AccessKey ID** and **AccessKey Secret** pair.
3.  Manually create a Persistent Volume \(PV\) and a Persistent Volume Claim \(PVC\).

## Prerequisites {#section_zjt_bzr_2gb .section}

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have connected to the Kubernetes cluster by using kubectl, see [Connect to a Kubernetes cluster by using kubectl](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).
-   You have created a bucket in the OSS console, see [Create a bucket](../../../../../reseller.en-US/Quick Start/Create a bucket.md#).

## Precautions {#section_hv4_klt_2gb .section}

-   Upgrading a Kubernetes cluster of Alibaba Cloud Container Service causes kubelet and the OSSFS driver to restart. As a result, the OSS directory becomes unavailable. In this case, the pods that use the OSS bucket must be recreated. We recommend that you add health check settings in the YAML file of your application. If you add health check settings for your application, the pods will be automatically restarted to remount the OSS bucket when the OSS directory within your container becomes unavailable.
-   If you use a Kubernetes cluster of the latest version, the preceding issue does not affect you.

## Create a PV {#section_f2g_pjs_2gb .section}

1.  Create a pv-oss.yaml file.

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
          bucket: "docker"                        ////Replace this value with your bucket name.
          url: "oss-cn-hangzhou.aliyuncs.com"     ////Replace this value with your URL.
          akId: "***"                             ////Replace this value with your AccessKey ID.
          akSecret: "***"                         ////Replace this value with your AccessKey Secret.
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"   ////Replace this value with your specified otherOpts value.
    ```

    **Parameter description**

    -   `alicloud-pvname`: indicates a PV name. This parameter value must be used in the `selector` field of the PVC associated with the PV.
    -   `bucket`: indicates a bucket name. Only buckets can be mounted to a Kubernetes cluster. The sub-directories or files in a bucket cannot be mounted to any Kubernetes cluster.
    -   `url`: indicates a domain name used to access the OSS bucket, namely, an endpoint. For more information, see [Regions and endpoints](../../../../../reseller.en-US/Developer Guide/Endpoint/Regions and endpoints.md#). You can also view the endpoint of the created OSS bucket in the OSS console. That is, log on to the OSS console, select the target bucket, and view **Endpoint** in the Domain Names area.
    -   `akId`: indicates your AccessKey ID. In the Container Service console, click ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/81395/154708382234867_en-US.png) in the upper-right corner. For a primary account, select **accesskeys**. For a RAM user, select **AccessKey**. Then, you can create your **AccessKey ID** and **AccessKey Secret**.
    -   `akSecret`: indicates your AccessKey Secret. Use the same method to obtain this parameter value as that to obtain the value of the `akId` parameter.
    -   `otherOpts`: indicates custom parameters for mounting the OSS bucket. Set this parameter in the format of `-o *** -o ***`. For more information, see [FAQ](../../../../../reseller.en-US/Tools/ossfs/FAQ.md#).
2.  Run the following command to create a PV:

    ```
    $ kubectl create -f pv-oss.yaml
    ```


**Result**

In the left-side navigation pane under Kubernetes, choose **Clusters** \> **Volumes**, and select the target cluster to view the created PV.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/81395/154708382234869_en-US.png)

## Create a PVC {#section_khn_mjt_2gb .section}

Create a PVC for the OSS bucket. Specifically, you need to set the`selector` field to filter for the created PV so that you can associate the PVC with the correct PV. Set the `storageClassName` parameter to associate the PVC with only the PV of the OSS type.

1.  Create a pvc-oss.yaml file.

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
    $ kubectl create -f pvc-oss.yaml
    ```


**Result**

In the left-side navigation pane under Kubernetes, choose **Application** \> **Volumes Claim**, and select the target cluster and namespace to view the created PVC.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/81395/154708382234871_en-US.png)

## Create an application {#section_lmz_dlt_2gb .section}

1.  Create an oss-static.yaml file.

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

    **Note:** For more information about `livenessProbe`, see [Use Alibaba Cloud OSS volumes](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage management/Use Alibaba Cloud OSS volumes.md#).

2.  Run the following command to create a deployment:

    ```
    $ kubectl create -f oss-static.yaml d
    ```


**Result**

In the left-side navigation pane under Kubernetes, choose **Application** \> **Deployment**, and select the target cluster and namespace to view the created deployment.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/81395/154708382234888_en-US.png)

## Verify that data in the OSS bucket are stored persistently {#section_mmk_gwt_2gb .section}

1.  Run the following command to view the pod in which the created deployment resides:

    ```
    $ kubectl get pod
    NAME                             READY   STATUS    RESTARTS   AGE
    oss-static-66fbb85b67-dqbl2      1/1     Running   0          1h
    ```

2.  Run the following command to view the files in the /data path:

    ```
    $ kubectl exec oss-static-66fbb85b67-dqbl2 ls /data | grep tmpfile
    ```

    **Note:** The /data path is empty.

3.  Run the following command to create a file named tmpfile in the /data path:

    ```
    $ kubectl exec oss-static-66fbb85b67-dqbl2 touch /data/tmpfile
    ```

4.  Run the following command to view the file in the /data path:

    ```
    $ kubectl exec oss-static-66fbb85b67-dqbl2 ls /data | grep tmpfile
    tmpfile
    ```

5.  Run the following command to remove the pod named oss-static-66fbb85b67-dqbl2:

    ```
    $ kubectl delete pod oss-static-66fbb85b67-dqbl2
    pod "oss-static-66fbb85b67-dqbl2" deleted
    ```

6.  Open another kubectl interface and run the following command to view the process in which the preceding pod is removed and a new pod is created by Kubernetes:

    ```
    $ kubectl get pod -w -l app=nginx
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

7.  Run the following command to view the pod created by Kubernetes:

    ```
    $ kubectl get pod
    NAME                             READY   STATUS    RESTARTS   AGE
    oss-static-66fbb85b67-zlvmw      1/1     Running   0          40s
    ```

8.  Run the following command to verify that the created file named tmpfile in the /data path has not been removed, indicating that data in the OSS bucket can be stored persistently:

    ```
    $ kubectl exec oss-static-66fbb85b67-zlvmw ls /data | grep tmpfile
    tmpfile
    ```


