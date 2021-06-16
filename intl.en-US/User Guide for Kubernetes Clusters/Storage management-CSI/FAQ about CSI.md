---
keyword: [disk volume, NAS volume, FAQ]
---

# FAQ about CSI

This topic describes how to troubleshoot common issues related to storage and how to resolve common issue related to disk volumes and NAS volumes.

## Troubleshoot common issues

Perform the following operations to view the log of a specified volume plug-in. This allows you to identify the problems.

1.  Run the following command to check whether events occur in persistent volume claims \(PVCs\) or pods:

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

2.  Run the following command to check whether the FlexVolume or CSI plug-in is deployed in the cluster:

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

3.  Check whether the volume templates match the template of the volume plug-in used in the cluster. The supported volume plug-ins are FlexVolume and CSI.

    If this is the first time you mount volumes in the cluster, check the driver specified in the persistent volume \(PV\) and StorageClass. The driver that you specified must be the same as the volume plug-in that is deployed in the cluster.

4.  Check whether the volume plug-in is upgraded to the latest version.

    -   Run the following command to query the image version of the FlexVolume plug-in:

        ```
        kubectl get ds flexvolume -nkube-system -oyaml | grep image
        ```

        Expected output:

        ```
        image: registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.8.109-649dc5a-aliyun
        ```

        For more information about the FlexVolume plug-in, see [Flexvolume](/intl.en-US/Release notes/System Component change Records/Storage/Flexvolume.md).

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

    -   If a PVC of disk type is in the Pending state, the cluster fails to create the PV. You must check the log of the Provisioner plug-in.
        -   If the FlexVolume plug-in is deployed in the cluster, run the following command to print the log of alicloud-disk-controller:

            ```
            podid=`kubectl get pod -nkube-system | grep alicloud-disk-controller | awk '{print $1}'`
            kubectl logs {PodID} -nkube-system
            ```

        -   If the CSI plug-in is deployed in the cluster, run the following command to print the log of csi-provisioner:

            ```
            podid=`kubectl get pod -nkube-system | grep csi-provisioner | awk '{print $1}'`
            kubectl logs {PodID} -nkube-system -c csi-provisioner
            ```

            **Note:** Two pods are created to run csi-provisioner. After you run the `kubectl get pod -nkube-system | grep csi-provisioner | awk '{print $1}'` command, two `podid` are returned. Then, run the `kubectl logs {PodID} -nkube-system -c csi-provisioner` command for each of the two pods.

    -   If a mounting error occurs when the system starts a pod, you must check the log of FlexVolume or csi-plugin.
        -   If the FlexVolume plug-in is deployed in the cluster, run the following command to print the log of FlexVolume:

            ```
            kubectl get pod {pod-name} -owide
            ```

            Log on to the Elastic Compute Service \(ECS\) instance where the pod runs and check the log of FlexVolume in the `/var/log/alicloud/flexvolume_**.log` directory.

        -   If the CSI plug-in is deployed in the cluster, run the following command to print the log of csi-plugin:

            ```
            nodeID=`kubectl get pod {pod-name} -owide | awk 'NR>1 {print $7}'`
            podID=`kubectl get pods -nkube-system -owide -lapp=csi-plugin | grep $nodeID|awk '{print $1}'`
            kubectl logs {PodID} -nkube-system
            ```

    -   View the log of Kubelet.

        Run the following command to query the node where the pod runs:

        ```
        kubectl get pod deployment-disk-5c795d7976-bjhkj -owide | awk 'NR>1 {print $7}'
        ```

        Log on to the node and check the log in the /var/log/message directory.


**Quick recovery**

If you fail to mount volumes to most of the pods on a node, you can schedule the pods to another node. For more information, see the questions and answers in the following sections.

## FAQ about disk volumes



## FAQ about NAS volumes



