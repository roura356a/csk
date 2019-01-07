# Use a static cloud disk when creating a stateful service {#concept_pql_5bv_cgb .concept}

This topic describes typical scenarios in which a static cloud disk is needed for creating a stateful service, and the procedure for how to use one.

## Scenarios and method {#section_grc_dhp_dgb .section}

**Scenarios for using cloud disks:**

-   You want to create applications that demand high disk I/O performance and do not require shared data. For example, MySQL, Redis, and other data storage services.
-   You want logs to be written at high speed.
-   You want your stored data to exist persistently. That is, the data still exist when the life cycle of the pod ends.

**Scenario for using static cloud disks:**

You have purchased a cloud disk.

**Method of using static cloud disks:**

Manually create a Persistent Volume \(PV\) and a Persistent Volume Claim \(PVC\).

## Prerequisites {#section_ekm_wcq_dgb .section}

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have created a cloud disk. For more information, see [Create a cloud disk](../../../../../reseller.en-US/User Guide/Cloud disks/Create a cloud disk.md#).
-   You have connected to the Kubernetes cluster by using kubectl, see [Connect to a Kubernetes cluster by using kubectl](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

## Limits {#section_ggg_s2q_dgb .section}

-   Cloud disks are the non-shared storage devices provided by the Alibaba Cloud Storage Team. Each cloud disk can be mounted to only one pod.
-   In a Kubernetes cluster, a cloud disk can be mounted only to a node that resides in the same zone as the cloud disk.

## Create a PV {#section_pcc_x2q_dgb .section}

1.  Create a pv-static.yaml file.

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: <your-disk-id>
      labels:
        alicloud-pvname: <your-disk-id>
        failure-domain.beta.kubernetes.io/zone: <your-zone>
        failure-domain.beta.kubernetes.io/region: <your-region>
    spec:
      capacity:
        storage: 20Gi
      accessModes:
        - ReadWriteOnce
      flexVolume:
        driver: "alicloud/disk"
        fsType: "ext4"
        options:
          volumeId: "<your-disk-id>"
    ```

    **Note:** 

    -   `alicloud-pvname: <your-disk-id>`: indicates the PV name. This parameter must be set to the same value as that of the `volumeID` parameter, namely, the cloud disk ID.
    -   `failure-domain.beta.kubernetes.io/zone: <your-zone>`: indicates the zone in which the cloud disk resides. For example, cn-hangzhou-b.
    -   `failure-domain.beta.kubernetes.io/region: <your-region>`: indicates the region in which the cloud disk resides. For example, cn-hangzhou.
    If you use a Kubernetes cluster that has multiple zones, you must set the `failure-domain.beta.kubernetes.io/zone` parameter and the `failure-domain.beta.kubernetes.io/region` parameter so that you can guarantee that your pod can be scheduled to the zone in which the cloud disk resides.

2.  Run the following command to create a PV:

    ```
    $ kubectl create -f pv-static.yaml
    ```


**Result**

In the left-side navigation pane under Kubernetes, choose **Clusters** \> **Volumes**, and select the target cluster to see the created PV.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79961/154684661234459_en-US.png)

## Create a PVC {#section_hww_rfr_dgb .section}

Create a PVC for the cloud disk. Specifically, you need to set the`selector` field to filter for the created PV so that you can associate the PVC with the correct PV.

1.  Create a pvc-static.yaml file.

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: pvc-disk
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
      selector:
        matchLabels:
          alicloud-pvname: <your-disk-id>
    ```

2.  Run the following command to create a PVC:

    ```
    $ kubectl create -f pvc-static.yaml
    ```


**Result**

In the left-side navigation pane under Kubernetes, choose **Application** \> **Volumes Claim**, and select the target cluster and namespace to see the created PVC.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79961/154684661234462_en-US.png)

## Create an application {#section_by4_wbs_dgb .section}

1.  Create a static.yaml file.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-static
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
            image: nginx
            volumeMounts:
              - name: disk-pvc
                mountPath: "/data"
          volumes:
            - name: disk-pvc
              persistentVolumeClaim:
                claimName: pvc-disk
    ```

2.  Run the following command to create a deployment:

    ```
    $ kubectl create -f static.yaml
    ```


**Result**

In the left-side navigation pane under Kubernetes, choose **Application** \> **Deployment**, and select the target cluster and namespace to see the created deployment.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79961/154684661234465_en-US.png)

## Persistent data storage on the static cloud disk {#section_wm4_cyx_dgb .section}

1.  Run the following command to view the pod in which the created deployment resides:

    ```
    $ kubectl get pod | grep static
    nginx-static-78c7dcb9d7-g9lll   2/2     Running     0          32s
    ```

2.  Run the following command to check whether the new cloud disk is mounted to the /data path:

    ```
    $ kubectl exec nginx-static-78c7dcb9d7-g9lll df | grep data
    /dev/vdf        20511312    45080  20449848   1% /data
    ```

3.  Run the following command to view the file in the /data path:

    ```
    $ kubectl exec nginx-static-78c7dcb9d7-g9lll ls /data
    lost+found
    ```

4.  Run the following command to create a file named static in the /data path:

    ```
    $ kubectl exec nginx-static-78c7dcb9d7-g9lll touch /data/static
    ```

5.  Run the following command to view the files in the /data path:

    ```
    $ kubectl exec nginx-static-78c7dcb9d7-g9lll ls /data
    static
    lost+found
    ```

6.  Run the following command to remove the pod named `nginx-static-78c7dcb9d7-g9lll`:

    ```
    $ kubectl delete pod nginx-static-78c7dcb9d7-g9lll
    pod "nginx-static-78c7dcb9d7-g9lll" deleted
    ```

7.  Open another kubectl interface and run the following command to view the process in which the preceding pod is removed and a new pod is created by Kubernetes:

    ```
    $ kubectl get pod -w -l app=nginx
    NAME                            READY   STATUS    RESTARTS   AGE
    nginx-static-78c7dcb9d7-g9lll   2/2     Running   0          50s
    nginx-static-78c7dcb9d7-g9lll   2/2   Terminating   0     72s
    nginx-static-78c7dcb9d7-h6brd   0/2   Pending   0     0s
    nginx-static-78c7dcb9d7-h6brd   0/2   Pending   0     0s
    nginx-static-78c7dcb9d7-h6brd   0/2   Init:0/1   0     0s
    nginx-static-78c7dcb9d7-g9lll   0/2   Terminating   0     73s
    nginx-static-78c7dcb9d7-h6brd   0/2   Init:0/1   0     5s
    nginx-static-78c7dcb9d7-g9lll   0/2   Terminating   0     78s
    nginx-static-78c7dcb9d7-g9lll   0/2   Terminating   0     78s
    nginx-static-78c7dcb9d7-h6brd   0/2   PodInitializing   0     6s
    nginx-static-78c7dcb9d7-h6brd   2/2   Running   0     8sg   0     8s
    ```

8.  Run the following command to view the new pod created by Kubernetes:

    ```
    $ kubectl get pod
    NAME                            READY   STATUS      RESTARTS   AGE
    nginx-static-78c7dcb9d7-h6brd   2/2     Running     0          14s
    ```

9.  Run the following command to verify that the created file named `static` in the /data path has not been removed, indicating that data in the static cloud disk can be stored persistently:

    ```
    $ kubectl exec nginx-static-78c7dcb9d7-h6brd ls /data
    static
    lost+found
    ```


