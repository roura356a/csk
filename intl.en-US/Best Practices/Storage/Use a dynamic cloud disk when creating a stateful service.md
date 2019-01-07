# Use a dynamic cloud disk when creating a stateful service {#concept_s12_3hy_dgb .concept}

This topic describes typical scenarios in which a dynamic cloud disk is needed for creating a stateful service, and the procedure for how to use one.

## Scenarios and method {#section_cmp_rhy_dgb .section}

**Scenario for using dynamic cloud disks:**

You want to configure the system to automatically purchase cloud disks when you deploy an application, rather than manually purchase cloud disks before deploying the application.

**Method of using a dynamic cloud disk:**

1.  Manually create a PVC and claim a specific StorageClass in the PVC.
2.  Use the StorageClass to enable the system to automatically create a PV when you deploy an application.

## Prerequisites {#section_ekm_wcq_dgb .section}

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have connected to the Kubernetes cluster by using kubectl, see [Connect to a Kubernetes cluster by using kubectl](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).
-   You have installed the provisioner plugin in the Kubernetes cluster. The plugin automatically creates a cloud disk according to a specific StorageClass.

## Provisioner plugin {#section_mkp_zcz_dgb .section}

When you create a cluster through Alibaba Cloud Container Service for Kubernetes, the provisioner plugin is installed in the cluster by default.

## Create a StorageClass {#section_rjp_kdz_dgb .section}

By default, Alibaba Cloud Container Service for Kubernetes creates four StorageClasses for a cluster during the cluster initialization, and the StorageClasses use the default settings. Furthermore, the four default StorageClasses are created only for a cluster that has a single zone. For a cluster that has multiple zones, you need to manually create a StorageClass. The following are the four StorageClasses created by default:

-   alicloud-disk-common indicates to automatically create a basic cloud disk.
-   alicloud-disk-efficiency indicates to automatically create an Ultra cloud disk.
-   alicloud-disk-ssd indicates to automatically create an SSD cloud disk.
-   alicloud-disk-available indicates a systematic method of disk selection. Specifically, the system first attempts to create an Ultra cloud disk. If the Ultra cloud disks in the specified zone are sold out, the system tries to create an SSD cloud disk. If the SSD cloud disks are sold out, the system tries to create a basic cloud disk.

1.  Create a storageclass.yaml file.

    ```
    kind: StorageClass
    apiVersion: storage.k8s.io/v1beta1
    metadata:
      name: alicloud-disk-ssd-hangzhou-b
    provisioner: alicloud/disk
    reclaimPolicy: Retain
    parameters:
      type: cloud_ssd
      regionid: cn-hangzhou
      zoneid: cn-hangzhou-b
      fstype: "ext4"
      readonly: "false"
    ```

    **Parameter setting**

    -   `provisioner`: Set this parameter to alicloud/disk to specify that the StorageClass creates an Alibaba Cloud cloud disk by using the provisioner plugin.
    -   `reclaimPolicy`: Set a policy to reclaim the cloud disk. Available values of this parameter are Delete and Retain. The default setting is Delete.

        **Note:** If you maintain the default setting, namely, Delete, the data on the cloud disk cannot be restored after you remove the PVC because the cloud disk is also removed.

    -   `type`: Specify a cloud disk type by using one the following values: cloud, cloud\_efficiency, cloud\_ssd, and available.
    -   `regionid`: \(optional\) Set the region in which the cloud disk is automatically created. This region must be the same as the region in which your cluster resides.
    -   `zoneid`: \(optional\) Set the zone in which a cloud disk is automatically created.
        -   If you set this parameter for a single-zone cluster, the value must be the same as the zone in which the cluster resides.
        -   If you set this parameter for a multi-zone cluster, multiple values can be set. For example,

            ```
            zoneid: cn-hangzhou-a,cn-hangzhou-b,cn-hangzhou-c
            ```

    -   `fstype`: \(optional\) Set the type of the file system used for automatic cloud disk creation. The default setting is ext4.
    -   `readonly`: \(optional\) Set whether the automatically created cloud disk is read only. If you set this parameter to true, the cloud disk can only be read. If you set this parameter to false, the cloud disk can be read and written. The default setting is false.
    -   `encrypted`: \(optional\) Set whether to encrypt the automatically created cloud disk. If you set this parameter to true, the cloud disk is encrypted. If you set this parameter to false, the cloud disk is not encrypted. The default setting is false.
2.  Run the following command to create a StorageClass:

    ```
    $ kubectl create -f storageclass.yaml
    ```


## Create a PVC {#section_sy5_fgz_dgb .section}

1.  Create a pvc-ssd.yaml file.

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: disk-ssd
    spec:
      accessModes:
        - ReadWriteOnce
      storageClassName: alicloud-disk-ssd-hangzhou-b
      resources:
        requests:
          storage: 20Gi
    ```

2.  Run the following command to create a PVC:

    ```
    $ kubectl create -f pvc-ssd.yaml
    ```


**Result**

In the left-side navigation pane under Kubernetes, choose **Application** \> **Volumes Claim**, and select the target cluster and namespace to see that the storage class name associated to the PVC is alicloud-disk-ssd-hangzhou-b specified in the `StorageClass`, and the PVC is associated with the volume.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/80612/154684690234507_en-US.png)

## Create an application {#section_fbc_wgz_dgb .section}

1.  Create a pvc-dynamic.yaml file.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-dynamic
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
                claimName: disk-ssd
    ```

2.  Run the following command to create a deployment:

    ```
    $ kubectl create -f nginx-dynamic.yaml
    ```


**Result**

In the left-side navigation pane under Kubernetes, choose **Application** \> **Deployment**, and select the target cluster and namespace to see the created deployment.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/80612/154684690234563_en-US.png)

## Persistent storage for a dynamic cloud disk {#section_ow1_chz_dgb .section}

1.  Run the following command to view the pod in which the created deployment resides:

    ```
    $ kubectl get pod | grep dynamic
    nginx-dynamic-5c74594ccb-zl9pf     2/2     Running     0          3m
    ```

2.  Run the following command to check whether a new cloud disk is mounted to the /data path:

    ```
    $ kubectl exec nginx-dynamic-5c74594ccb-zl9pf df | grep data
    /dev/vdh        20511312    45080  20449848   1% /data
    ```

3.  Run the following command to view the file in the /data path:

    ```
    $ kubectl exec nginx-dynamic-5c74594ccb-zl9pf ls /data
    lost+found
    ```

4.  Run the following command to create a file named dynamic in the /data path:

    ```
    $ kubectl exec nginx-dynamic-5c74594ccb-zl9pf touch /data/dynamic
    ```

5.  Run the following command to view the files in the /data path:

    ```
    $ kubectl exec nginx-dynamic-5c74594ccb-zl9pf ls /data
    dynamic
    lost+found
    ```

6.  Run the following command to remove the pod named `nginx-dynamic-78c7dcb9d7-g9lll`:

    ```
    $ kubectl delete pod nginx-dynamic-5c74594ccb-zl9pf
    pod "nginx-dynamic-5c74594ccb-zl9pf" deleted
    ```

7.  Open another kubectl interface and run the following command to view the process in which the preceding pod is removed and a new pod is created by Kubernetes:

    ```
    $ kubectl get pod -w -l app=nginx
    NAME                               READY   STATUS    RESTARTS   AGE
    nginx-dynamic-5c74594ccb-zl9pf     2/2     Running   0          6m48s
    nginx-dynamic-5c74594ccb-zl9pf   2/2   Terminating   0     7m32s
    nginx-dynamic-5c74594ccb-45sd4   0/2   Pending   0     0s
    nginx-dynamic-5c74594ccb-45sd4   0/2   Pending   0     0s
    nginx-dynamic-5c74594ccb-45sd4   0/2   Init:0/1   0     0s
    nginx-dynamic-5c74594ccb-zl9pf   0/2   Terminating   0     7m32s
    nginx-dynamic-5c74594ccb-zl9pf   0/2   Terminating   0     7m33s
    nginx-dynamic-5c74594ccb-zl9pf   0/2   Terminating   0     7m33s
    nginx-dynamic-5c74594ccb-45sd4   0/2   PodInitializing   0     5s
    nginx-dynamic-5c74594ccb-45sd4   2/2   Running   0     22s
    ```

8.  Run the following command to view the pod newly created by Kubernetes:

    ```
    $ kubectl get pod 
    NAME                               READY   STATUS      RESTARTS   AGE
    nginx-dynamic-5c74594ccb-45sd4     2/2     Running     0          2m
    ```

9.  Run the following command to verify that the created file named `dynamic` in the /data path has not been removed, indicating that data in the dynamic cloud disk can be stored persistently:

    ```
    $ kubectl exec nginx-dynamic-5c74594ccb-45sd4 ls /data
    dynamic
    lost+found
    ```


