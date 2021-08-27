---
keyword: [automatically expand a disk volume, expansion policy]
---

# Automatically expand a disk volume \(public preview\)

You can define one or more expansion policies to automatically expand a disk volume when the utilization of the disk volume exceeds a specified threshold value. This topic describes how to automatically expand a disk volume.

-   A Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The storage-operator component is installed in the cluster and the component version is V1.18.8.28-18cca7b-aliyun or later. For more information, see [storage-operator](/intl.en-US/Release notes/System Component change Records/Storage/storage-operator.md).
-   Your machine is connected to the cluster by using kubectl. For more information, see [Step 2: Select a type of cluster credentials](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

## Precautions

-   You can expand disks but cannot shrink disks.
-   You can expand only disks that are mounted to pods.
-   You can expand only disks that can be resized. For more information, see [ResizeDisk](/intl.en-US/API Reference/Disk/ResizeDisk.md).
-   The maximum time interval between two expansion operations is 2 minutes. The disk expansion process requires 1 minute. Make sure that you do not exhaust the disk space within 3 minutes.
-   The maximum disk capacity is 32 TiB.

## Limits

This feature is available only in the Malaysia \(Kuala Lumpur\) and China \(Beijing\) regions.

## Step 1: Configure an auto expansion policy

1.  Create a StorageClass.

    Skip this step if a StorageClass is already created.

    1.  Use the following template to create a file named storageclass.yaml.

        ```
        apiVersion: storage.k8s.io/v1
        kind: StorageClass
        metadata:
          name: alicloud-disk-essd
        provisioner: diskplugin.csi.alibabacloud.com
        parameters:
          type: cloud_essd
        reclaimPolicy: Delete
        allowVolumeExpansion: true
        ```

    2.  Run the following command to create a StorageClass:

        ```
        kubectl create -f storageclass.yaml
        ```

2.  Use a CustomResourceDefinition \(CRD\) to create an auto expansion policy.

    1.  Use the following template to create a file named StorageAutoScalerPolicy.yaml.

        ```
        apiVersion: storage.alibabacloud.com/v1alpha1
        kind: StorageAutoScalerPolicy
        metadata:
          name: hybrid-expand-policy
        spec:
          pvcSelector:
            matchLabels:
              app: mysql
          namespaces:
            - default
            - mysql
          conditions:
            - name: condition1
              key: volume-capacity-used-percentage
              operator: Gt
              values:
                - "80"
          actions:
            - name: action1
              type: volume-expand
              params:
                scale: 50Gi
                limits: 100Gi
            - name: action2
              type: volume-expand
              params:
                scale: 50%
                limits: 300Gi
        ```

        |Parameter|Description|
        |---------|-----------|
        |pvcSelector|Selects a persistent volume claim \(PVC\) based on labels. In this example, mysql is used.|
        |namespaces|Specifies the namespace of the PVC. If multiple namespaces are specified, the logical operator between the namespaces is OR. Default value: default. In this example, default and mysql are specified.|
        |conditions|Specifies the conditions that trigger the action. The logical operator between multiple conditions is AND. Each condition includes the following parameters:        -   key: specifies the type of the metric.
        -   volume-capacity-used-percentage: specifies that the capacity is expressed in percentage.
        -   operator: specifies the operator, which can be Gt \(greater than\), Lt \(less than\), Eq \(equal to\), or Ne \(not equal to\). This parameter value is not case-sensitive.
        -   values: specifies the threshold value.
In the example, the condition specifies that the action is triggered when the capacity utilization of the PVC exceeds 80%. |
        |actions|Specifies the operations that are performed when the preceding conditions are met. Multiple operations are allowed. Each action includes the following parameters:        -   type: specifies the type of action. Only volume-expand is supported.
        -   scale: specifies the size of capacity that you want to add to the disk. Valid units: GiB and percentage \(%\).
        -   limits: specifies the maximum disk capacity of the PVC if the action is performed.
If multiple actions are specified in the actions section, the first action that meets the limits is performed. The other actions are skipped.

        -   In this example, action1 specifies the disk is expanded by 50 GiB if the disk size is no greater than 100 GiB. The maximum disk size is 100 GiB.
        -   In this example, action2 specifies that the disk is expanded by 50% if the disk size is greater than 100 GiB but smaller than 300 GiB. The maximum disk size is 300 GiB. |

    2.  Run the following command to create an auto expansion policy:

        ```
        kubectl create -f StorageAutoScalerPolicy.yaml
        ```

3.  Create a StatefulSet.

    1.  Use the following template to create a file named StatefulSet.yaml.

        ```
        apiVersion: apps/v1
        kind: StatefulSet
        metadata:
          name: web
        spec:
          selector:
            matchLabels:
              app: mysql
          serviceName: "nginx"
          replicas: 3
          template:
            metadata:
              labels:
                app: mysql
            spec:
              containers:
                - name: nginx
                  image: nginx:1.14.2
                  ports:
                    - containerPort: 80
                      name: web
                  volumeMounts:
                    - name: www
                      mountPath: /usr/share/nginx/html
          volumeClaimTemplates:
            - metadata:
                name: www
                labels:
                  app: mysql
              spec:
                accessModes: [ "ReadWriteOnce" ]
                storageClassName: "alicloud-disk-essd"
                resources:
                  requests:
                    storage: 25Gi
        ```

    2.  Run the following command to create a StatefulSet:

        ```
        kubectl create -f StatefulSet.yaml
        ```


## Step 2: Verify automatic disk expansion

1.  Write data to the mounted directory to increase the disk utilization rate to more than 80%.

    1.  Run the following command to write data to the mounted directory:

        ```
        dd if=<data path> of=<mounted directory>
        ```

    2.  Run the following command to query the disk details:

        ```
        df -h | grep d-****1
        ```

        Expected output:

        ```
        Filesystem    Size   Used   Avail   Use%    Mounted on
        /dev/vde      25G    24G    1.5G    95%     /var/lib/kubelet/plugins/kubernetes.io/csi/pv/d-****1/globalmount
        ```

2.  Run the following command to query events that are related to disk expansion:

    Disk expansion is triggered when the disk utilization rate exceeds 80%. In this case, **action1** meets the limits. Therefore, **action1** is performed to expand the disk.

    ```
    kubectl get events
    ```

    Expected output:

    ```
    101s     Warning     StartExpand                persistentvolumeclaim/www-web-1     Start to expand of pvc www-web-1 from 25Gi to 75Gi, usedCapacityPercentage:94%, freeSize:1472MB.
    101s     Warning     ExternalExpanding          persistentvolumeclaim/www-web-1     Ignoring the PVC: did't find a plugin capable of expanding the volume; waiting for an external controller to process this PVC.
    101s     Normal      Resizing                   persistentvolumeclaim/www-web-1     External resizer is resizing volume d-****1
    97s      Normal      FileSystemResizeRequired   persistentvolumeclaim/www-web-1     Require file system resize of volume on node
    96s      Warning     SkipExpand                 persistentvolumeclaim/www-web-1     Pvc www-web-1 is expanding status from 25Gi to 75Gi, this action action2 will skip.
    ```

3.  Run the following command to query the capacity of the PVC:

    ```
    kubectl get pvc
    ```

    Expected output:

    ```
    NAME        STATUS     VOLUME     CAPACITY     ACCESS MODES     STORAGECLASS          AGE
    www-web-0   Bound      d-****0    25Gi         RWO              alicloud-disk-essd    22m
    www-web-1   Bound      d-****1    75Gi         RWO              alicloud-disk-essd    21m
    www-web-2   Bound      d-****2    25Gi         RWO              alicloud-disk-essd    21m
    ```

    The expected output shows that the disk `d-****1` is expanded to 75 GiB.

4.  Repeat [Step 1](#step_s8r_zb8_4bj) to increase the disk utilization rate to more than 80%. This expands the disk again.

    Run the following command to query events that are related to disk expansion:

    ```
    kubectl get events
    ```

    Expected output:

    ```
    6m4s     Warning     StartExpand                persistentvolumeclaim/www-web-1     Start to expand of pvc www-web-1 from 75Gi to 100Gi, usedCapacityPercentage:95%, freeSize:3732MB.
    5m2s     Warning     ExternalExpanding          persistentvolumeclaim/www-web-1     Ignoring the PVC: did't find a plugin capable of expanding the volume; waiting for an external controller to process this PVC.
    2m4s     Normal      Resizing                   persistentvolumeclaim/www-web-1     External resizer is resizing volume d-****1
    3m4s     Normal      FileSystemResizeRequired   persistentvolumeclaim/www-web-1     Require file system resize of volume on node
    5m59s    Warning     SkipExpand                 persistentvolumeclaim/www-web-1     Pvc www-web-1 is expanding status from 75Gi to 100Gi, this action action2 will skip.
    ```

    The limits parameter of action1 specifies that the maximum disk capacity is 100 GiB. Therefore, the disk size is expanded from 75 GiB to 100 GiB, as shown in the expected output.

5.  Repeat [Step 1](#step_s8r_zb8_4bj) to increase the disk utilization rate to more than 80%. This expands the disk a third time.

    Run the following command to query events that are related to disk expansion:

    ```
    kubectl get events
    ```

    Expected output:

    ```
    7m22s    Warning     StartExpand                persistentvolumeclaim/www-web-1     Start to expand of pvc www-web-1 from 100Gi to 150Gi, usedCapacityPercentage:95%, freeSize:3732MB.
    5m2s     Warning     ExternalExpanding          persistentvolumeclaim/www-web-1     Ignoring the PVC: did't find a plugin capable of expanding the volume; waiting for an external controller to process this PVC.
    2m4s     Normal      Resizing                   persistentvolumeclaim/www-web-1     External resizer is resizing volume d-****1
    3m4s     Normal      FileSystemResizeRequired   persistentvolumeclaim/www-web-1     Require file system resize of volume on node
    5m59s    Warning     SkipExpand                 persistentvolumeclaim/www-web-1     Pvc www-web-1 is expanding status from 100Gi to 150Gi, this action action1 will skip.
    ```

    The limits parameter of action1 is 100 GiB. Therefore, this action is not performed. The expected output shows that action2 is performed to expand the disk size from 100 GiB to 150 GiB.

6.  Repeat [Step 1](#step_s8r_zb8_4bj) to increase the disk utilization rate to more than 80%. This expands the disk a fourth time.

    Run the following command to query events that are related to disk expansion:

    ```
    kubectl get events
    ```

    Expected output:

    ```
    0s     Warning     StartExpand                persistentvolumeclaim/www-web-1     Start to expand of pvc www-web-1 from 150Gi to 225Gi, usedCapacityPercentage:94%, freeSize:7637MB.
    0s     Warning     ExternalExpanding          persistentvolumeclaim/www-web-1     Ignoring the PVC: did't find a plugin capable of expanding the volume; waiting for an external controller to process this PVC.
    0s     Normal      Resizing                   persistentvolumeclaim/www-web-1     External resizer is resizing volume d-****1
    0s     Normal      FileSystemResizeRequired   persistentvolumeclaim/www-web-1     Require file system resize of volume on node
    0s     Warning     SkipExpand                 persistentvolumeclaim/www-web-1     Pvc www-web-1 is expanding status from 150Gi to 225Gi, this action action1 will skip.
    ```

    The expected output shows that action2 is performed to expand the disk size from 150 GiB to 225 GiB.

7.  Repeat [Step 1](#step_s8r_zb8_4bj) to increase the disk utilization rate to more than 80%. This expands the disk a fifth time.

    Run the following command to query events that are related to disk expansion:

    ```
    kubectl get events
    ```

    Expected output:

    ```
    0s     Warning     StartExpand                 persistentvolumeclaim/www-web-1     Start to expand of pvc www-web-1 from 225Gi to 300Gi, usedCapacityPercentage:94%, freeSize:7637MB.
    0s     Warning     ExternalExpanding           persistentvolumeclaim/www-web-1     Ignoring the PVC: did't find a plugin capable of expanding the volume; waiting for an external controller to process this PVC.
    0s     Normal      Resizing                    persistentvolumeclaim/www-web-1     External resizer is resizing volume d-****1
    0s     Normal      FileSystemResizeRequired    persistentvolumeclaim/www-web-1     Require file system resize of volume on node
    0s     Warning     FileSystemResizeSuccessful  persistentvolumeclaim/www-web-1     MountVolume.NodeExpandVolume succeeded for volume "d-****1"
    ```

    The limits parameter of action2 specifies that the maximum disk capacity is 300 GiB. Therefore, the disk size is expanded from 225 GiB to 300 GiB, as shown in the expected output.

    Disk expansion is not triggered when the disk utilization rate exceeds 80% again.


