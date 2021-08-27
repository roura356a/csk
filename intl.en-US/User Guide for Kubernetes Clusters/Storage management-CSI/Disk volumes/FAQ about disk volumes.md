---
keyword: [disk volumes, FAQ, frequently asked questions]
---

# FAQ about disk volumes

This topic provides answers to some frequently asked questions about disk volumes.

-   [Why does the system prompt The specified disk is not a portable disk when I unmount a disk?](#section_ddz_m1r_muj)
-   [Why does the system prompt had volume node affinity conflict when I launch a pod that has a disk mounted?](#section_e36_d7y_yvn)
-   [Why does the system prompt can't find disk when I launch a pod that has a disk mounted?](#section_44d_enm_5qg)
-   [Why does the system prompt The specified AZone inventory is insufficient when I create a dynamically provisioned PV?](#section_hxz_s8n_psd)
-   [Why does the system prompt disk size is not supported when I create a dynamically provisioned PV?](#section_wfu_6qp_5yd)
-   [What do I do when the disk is blocked, which results in a pod launch failure that lasts for a long period of time?](#section_u8n_7uh_0zb)
-   [Why does the "FailedMount" warning appear when I start a pod?](#section_mwn_qnc_5o8)
-   [Why do I do when I fail to delete a pod and kubelet generates pod logs that are not managed by Container Service for Kubernetes \(ACK\)?](#section_8pk_40e_0n1)
-   [Why does the system prompt that the mounting fails and cannot be recovered when a pod failed to restart after deletion?](#section_vse_dem_l0i)

## Why does the system prompt The specified disk is not a portable disk when I unmount a disk?

Symptom:

The system prompts The specified disk is not a portable disk when you unmount a disk.

Cause:

The disk is billed on a subscription basis, or you accidentally switched the billing method of the disk to subscription when you upgraded the Elastic Compute Service \(ECS\) instance that is associated with the disk.

Solution:

Switch the billing method of the disk from subscription to pay-as-you-go.

## Why does the system prompt had volume node affinity conflict when I launch a pod that has a disk mounted?

Symptom:

Your attempt to launch a pod that has a disk mounted failed and the system prompts had volume node affinity conflict.

Cause:

You have set the `nodeaffinity` attribute for the persistent volume \(PV\) that is used to mount the disk. The value of the attribute is different from that of the pod. As a result, the pod cannot be scheduled to an appropriate node.

Solution:

Modify the nodeaffinity attribute of the PV or pod to ensure that the PV and pod use the same value.

## Why does the system prompt can't find disk when I launch a pod that has a disk mounted?

Symptom:

The system prompts The specified disk is not a portable disk when you unmount a disk.

Cause:

-   You entered an invalid value of the DiskID parameter in the PV configurations.
-   Your account does not have the permissions to modify the DiskID parameter. The disk may not belong to the current account.

Solution:

Modify the DiskID parameter.

## Why does the system prompt The specified AZone inventory is insufficient when I create a dynamically provisioned PV?

Symptom:

Your attempt to create a dynamically provisioned PV failed and the system prompts a persistent volume claim \(PVC\) event: The specified AZone inventory is insufficient.

Cause:

The system failed to create the disk because ECS instances are out of stock.

Solution:

Change the type of disk or select another zone.

## Why does the system prompt disk size is not supported when I create a dynamically provisioned PV?

Symptom:

Your attempt to create a dynamically provisioned PV failed and the system prompts disk size is not supported.

Cause:

The size of the disk that you specified in the PVC is invalid. The disk size must be at least 20 GiB.

Solution:

Change the size of the disk that is specified in the PVC to a valid value.

## What do I do when the disk is blocked, which results in a pod launch failure that lasts for a long period of time?

Symptom:

The disk is blocked and the following errors are reported in the kubelet log:

```
Operation for "{volumeName:kubernetes.io/csi/diskplugin.csi.alibabacloud.com^d-2zejaz33icbp2vvv**** podName: nodeName:}" failed. 
No retries permitted until 2020-11-05 14:38:12.653566679 +0800 CST m=+9150650.781033052 (durationBeforeRetry 2m2s). 
Error: "MountVolume.MountDevice failed for volume \"d-2zejaz33icbp2vvv****\" (UniqueName: \"kubernetes.io/csi/diskplugin.csi.alibabacloud.com^d-2zejaz33icbp2vvv****\") pod \"pod-e5ee2d454cdb4d1d916d933495e56cbe-358****\" (UID: \"f8d71e90-d934-4d5a-b54f-62555da5****\") : rpc error: code = Aborted desc = NodeStageVolume: Previous attach action is still in process
```

Cause:

You used an earlier version of the Container Storage Interface \(CSI\) plug-in. Earlier versions of the CSI plug-in use the BlkID parameter to obtain the UID of the disk. The disks that are restored from snapshots share the same UID. This blocks the BlkID command.

Solution:

Restart the current node, or upgrade CSI to the latest version.

## Why does the "FailedMount" warning appear when I start a pod?

Symptom:

The following warning appears when you start a pod:

```
Warning  FailedMount       104s                 kubelet, cn-zhangjiakou.172.20.XX.XX  Unable to attach or mount volumes: unmounted volumes=[sysdata-nas], unattached volumes=[kun-log kun-script kun-app sysdata-nas kun-patch default-token-rbx8p kun-etc kun-bin]: timed out waiting for the condition
Warning  FailedMount       98s (x9 over 3m45s)  kubelet, cn-zhangjiakou.172.20.XX.XX  MountVolume.MountDevice failed for volume "nas-9d9ead08-8a1d-4463-a7e0-7bd0e3d3****" : kubernetes.io/csi: attacher.MountDevice failed to create newCsiDriverClient: driver name nasplugin.csi.alibabacloud.com not found in the list of registered CSI drivers
```

Cause:

The warning usually appears on newly created nodes. The system starts the CSI pods and the service pods at the same time, and it requires a period of time to register CSI. Therefore, the CSI registration may not have been completed when you mount a volume to a service pod. This triggers the warning.

Solution:

No operation is required. The warning does not affect the startup of the pod.

## Why do I do when I fail to delete a pod and kubelet generates pod logs that are not managed by Container Service for Kubernetes \(ACK\)?

Symptom:

Your attempt to delete a pod failed and kubelet generates pod logs that are not managed by ACK.

Cause:

The pod exceptionally exits. Some mount points are not deleted when the system deletes the pod. As a result, the system fails to delete the pod. The garbage collection process of kubelet cannot collect all volume garbage. You must remove invalid mount points manually or by executing an automated script.

Solution:

Run the following script on the failed node to remove invalid mount points:

```
wget https://raw.githubusercontent.com/AliyunContainerService/kubernetes-issues-solution/master/kubelet/kubelet.sh
sh kubelet.sh
```

## Why does the system prompt that the mounting fails and cannot be recovered when a pod failed to restart after deletion?

Symptom:

The pod cannot be recreated after deletion and the system prompts the following error and the error cannot be automatically fixed:

```
Warning FailedMount 9m53s (x23 over 40m) kubelet MountVolume.SetUp failed for volume "xxxxx" : rpc error: code = Internal desc = stat /var/lib/kubelet/plugins/kubernetes.io/csi/pv/xxxxx/globalmount: no such file or directory
```

Impact scope:

-   The Kubernetes version of your cluster is 1.20.4-aliyun-1.
-   Your application is mounted with disks.
-   StatefulSets are used and the `podManagementPolicy: "Parallel"` attribute is configured.

Cause:

For more information, see [Pod fails to start after restarting rapidly](https://github.com/kubernetes/kubernetes/issues/102779).

Solution:

-   Add new nodes to the cluster and then remove all original nodes. The pod will be automatically recreated. For more information, see [Scale out a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Manage node pools.md) and [Remove nodes from an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Remove nodes from an ACK cluster.md).
-   Change the pod management policy of the StatefulSet to `orderedready` or remove the `podManagementPolicy: "Parallel"` attribute.
-   If the cluster contains a small number of nodes, use the following solution:
    1.  Add the `cordon` label to the node where the pod is deployed to set the node as unschedulable.
    2.  Delete the pod and wait until the state of the pod changes to Pending.
    3.  Remove the `cordon` label from the node and wait for the pod to restart.
-   If the cluster contains a large number of nodes, schedule the pod to another node. Then, the pod can be restarted as normal.

