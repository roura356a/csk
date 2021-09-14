---
keyword: [disk volume, NAS volume, FAQ]
---

# FAQ about CSI

This topic describes how to troubleshoot common issues related to storage and provides answers to some frequently asked questions about disk volumes and Apsara File Storage NAS \(NAS\) volumes.

|Type|Question|
|----|--------|
|Troubleshoot common issues|[Troubleshoot common issues](#section_1ym_k67_i1r)|
|FAQ about disk volumes|-   [Why does the system prompt The specified disk is not a portable disk when I unmount a disk?](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/FAQ about disk volumes.md)
-   [Why does the system prompt had volume node affinity conflict when I launch a pod that has a disk mounted?](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/FAQ about disk volumes.md)
-   [Why does the system prompt can't find disk when I launch a pod that has a disk mounted?](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/FAQ about disk volumes.md)
-   [Why does the system prompt The specified AZone inventory is insufficient when I create a dynamically provisioned PV?](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/FAQ about disk volumes.md)
-   [Why does the system prompt disk size is not supported when I create a dynamically provisioned PV?](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/FAQ about disk volumes.md)
-   [What do I do when the disk is blocked, which results in a pod launch failure that lasts for a long period of time?](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/FAQ about disk volumes.md)
-   [Why does the "FailedMount" warning appear when I start a pod?](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/FAQ about disk volumes.md)
-   [Why do I do when I fail to delete a pod and kubelet generates pod logs that are not managed by Container Service for Kubernetes \(ACK\)?](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/FAQ about disk volumes.md)
-   [Why does the system prompt that the mounting fails and cannot be recovered when a pod failed to restart after deletion?](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/FAQ about disk volumes.md) |
|FAQ about NAS volumes|-   [Why does the system prompt chown: option not permitted when I mount a NAS file system?](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/FAQ about NAS volumes.md)
-   [What do I do if the task queue of alicloud-nas-controller is full and PVs cannot be created when I use a dynamically provisioned NAS volume?](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/NAS volumes/FAQ about NAS volumes.md)
-   [Why does it require a long time to mount a NAS volume?](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/FAQ about NAS volumes.md) |

## Troubleshoot common issues

Perform the following operations to view the log of a specified volume plug-in. This allows you to locate issues.

1.  Run the following command to check whether events related to persistent volume claims \(PVCs\) or pods were generated:

    ```
    kubectl get events
    ```

    Expected output:

    ```
    LAST SEEN   TYPE      REASON                 OBJECT                                                  MESSAGE
    2m56s       Normal    FailedBinding          persistentvolumeclaim/data-my-release-mariadb-0         no persistent volumes available for this claim and no storage class is set
    41s         Normal    ExternalProvisioning   persistentvolumeclaim/pvc-nas-dynamic-create-subpath8   waiting for a volume to be created, either by external provisioner "nasplugin.csi.alibabacloud.com" or manually created by system administrator
    3m31s       Normal    Provisioning           persistentvolumeclaim/pvc-nas-dynamic-create-subpath8   External provisioner is provisioning volume for claim "default/pvc-nas-dynamic-create-subpath8"
    ```

2.  Check whether the FlexVolume or CSI plug-in is deployed in the cluster.

    -   Run the following command to check whether the FlexVolume plug-in is deployed in the cluster:

        ```
        kubectl get pod -nkube-system |grep flexvolume
        ```

        Expected output:

        ```
        NAME                      READY   STATUS             RESTARTS   AGE
        flexvolume-***            4/4     Running            0          23d
        ```

    -   Run the following command to check whether the CSI plug-in is deployed in the cluster:

        ```
        kubectl get pod -nkube-system |grep csi
        ```

        Expected output:

        ```
        NAME                       READY   STATUS             RESTARTS   AGE
        csi-plugin-***             4/4     Running            0          23d
        csi-provisioner-***        7/7     Running            0          14d
        ```

3.  Check whether the volume template matches the template of the volume plug-in used in the cluster. The supported volume plug-ins are FlexVolume and CSI.

    If this is the first time you mount volumes in the cluster, check whether the driver specified in the persistent volume \(PV\) and StorageClass is a CSI driver or a FlexVolume driver. The name of the driver that you specified must be the same as the type of the volume plug-in that is deployed in the cluster.

4.  Check whether the volume plug-in is upgraded to the latest version.

    -   Run the following command to query the image version of the FlexVolume plug-in:

        ```
        kubectl get ds flexvolume -nkube-system -oyaml | grep image
        ```

        Expected output:

        ```
        image: registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.8.109-649dc5a-aliyun
        ```

        For more information about the FlexVolume plug-in, see [FlexVolume](/intl.en-US/Release notes/System Component change Records/Storage/FlexVolume.md).

    -   Run the following command to query the image version of the CSI plug-in:

        ```
        kubectl get ds csi-plugin -nkube-system -oyaml |grep image
        ```

        Expected output:

        ```
        image: registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.45-1c5d2cd1-aliyun
        ```

        For more information about the CSI plug-in, see [csi-plugin](/intl.en-US/Release notes/System Component change Records/Storage/csi-plugin.md) and [csi-provisioner](/intl.en-US/Release notes/System Component change Records/Storage/csi-provisioner.md).

5.  View log data.

    -   If a PVC of disk type is in the Pending state, it indicates that the related PV is not created. You must check the log of the Provisioner plug-in.
        -   If the FlexVolume plug-in is deployed in the cluster, run the following command to print the log of alicloud-disk-controller:

            ```
            podid=`kubectl get pod -nkube-system | grep alicloud-disk-controller | awk '{print $1}'`
            kubectl logs <PodID> -nkube-system
            ```

        -   If the CSI plug-in is deployed in the cluster, run the following command to print the log of csi-provisioner:

            ```
            podid=`kubectl get pod -nkube-system | grep csi-provisioner | awk '{print $1}'`
            kubectl logs <PodID> -nkube-system -c csi-provisioner
            ```

            **Note:** Two pods are created to run csi-provisioner. After you run the `kubectl get pod -nkube-system | grep csi-provisioner | awk '{print $1}'` command, two `podid` are returned. Then, run the `kubectl logs <PodID> -nkube-system -c csi-provisioner` command on each pod.

    -   If a mounting error occurs when the system starts a pod, you must check the log of FlexVolume or csi-plugin.
        -   If the FlexVolume plug-in is deployed in the cluster, run the following command to print the log of FlexVolume:

            ```
            kubectl get pod <pod-name> -owide
            ```

            Log on to the Elastic Compute Service \(ECS\) instance where the pod runs and check the log of FlexVolume in the `/var/log/alicloud/flexvolume_**.log` directory.

        -   If the CSI plug-in is deployed in the cluster, run the following command to print the log of csi-plugin:

            ```
            nodeID=`kubectl get pod <pod-name> -owide | awk 'NR>1 {print $7}'`
            podID=`kubectl get pods -nkube-system -owide -lapp=csi-plugin | grep $nodeID|awk '{print $1}'`
            kubectl logs <PodID> -nkube-system
            ```

    -   View the log of kubelet.

        Run the following command to query the node on which the pod runs:

        ```
        kubectl get pod <pod-name> -owide | awk 'NR>1 {print $7}'
        ```

        Log on to the node and check the log files in the /var/log/message directory.


**Quick recovery**

If you fail to mount volumes to most of the pods on a node, you can schedule the pods to other nodes. For more information, see [Schedule pods to specific nodes](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Application Scheduling Deployment/Schedule pods to specific nodes.md).

