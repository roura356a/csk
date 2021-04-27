---
keyword: [disk storage, NAS storage, FAQ]
---

# FAQ about storage services

This topic provides answers to some frequently asked questions about storage services.

**FAQ about disk storage**

-   [Why does the system prompt "The specified disk is not a portable disk" when I unmount a disk?](#section_rqz_dv4_up0)
-   [Why does the system prompt "had volume node affinity conflict" when I launch a pod that has a disk mounted?](#section_24n_fqd_1lz)
-   [Why does the system prompt "can't find disk" when I launch a pod that has a disk mounted?](#section_3et_ci6_kef)
-   [Why does the system prompt "The specified AZone inventory is insufficient" when I dynamically provision a PV?](#section_0as_22b_txn)
-   [Why does the system prompt "disk size is not supported" when I dynamically provision a PV?](#section_ony_za8_acw)
-   [Why is the disk blocked which results a failure to launch the pod for a long period of time?](#section_rv9_dh1_7r1)
-   [Why does the "FailedMount" warning appear when I start the pod?](#section_vv3_6ll_hvn)
-   [Why do I fail to delete a pod and Kubelet generates a pod log that is not managed by Container Service for Kubernetes \(ACK\)?](#section_u64_r44_iej)

**FAQ about NAS storage**

[Why does the system prompt "chown: option not permitted" when I mount an Apsara File Storage NAS \(NAS\) file system?](#section_cxn_2gp_hxw)

## Why does the system prompt "The specified disk is not a portable disk" when I unmount a disk?

Issue:

The system prompts "The specified disk is not a portable disk" when you unmount a disk.

Cause:

The disk is billed on a subscription basis, or you accidentally switch the billing method of the disk to subscription when you upgrade the Elastic Compute Service \(ECS\) instance that is associated with the disk.

Solution:

Switch the billing method of the disk from subscription to pay-as-you-go.

## Why does the system prompt "had volume node affinity conflict" when I launch a pod that has a disk mounted?

Issue:

You fail to launch a pod that has a disk mounted and the system prompts "had volume node affinity conflict".

Cause:

You have set the nodeaffinity attribute for the persistent volume \(PV\). The value of the attribute is different from that of the pod. Therefore, the pod cannot be scheduled to the expected node.

Solution:

Modify the nodeaffinity attribute of the PV or pod to ensure that the PV and pod use the same value.

## Why does the system prompt "can't find disk" when I launch a pod that has a disk mounted?

Issue:

When you launch a pod that has a disk mounted, the system prompts "can't find disk".

Cause:

-   You entered an invalid value for diskid when you set the parameters of the PV.
-   Your account does not have permissions to modify diskid. The disk may not belong to the current account.

Solution:

Modify the diskid parameter.

## Why does the system prompt "The specified AZone inventory is insufficient" when I dynamically provision a PV?

Issue:

You fail to dynamically provision a PV and the system prompts "The specified AZone inventory is insufficient".

Cause:

The system fails to create the disk because ECS instances are out of stock.

Solution:

Change the type of disk or select another zone.

## Why does the system prompt "disk size is not supported" when I dynamically provision a PV?

Issue:

You fail to dynamically provision a PV and the system prompts "disk size is not supported".

Cause:

The size of the disk that you specified in the PVC is invalid. The disk size must be at least 20 GiB.

Solution:

Change the size of the disk that is specified in the PVC to a valid value.

## Why is the disk blocked which results a failure to launch the pod for a long period of time?

Issue:

The disk is blocked and the following errors are reported in the kubelet log:

```
Operation for "{volumeName:kubernetes.io/csi/diskplugin.csi.alibabacloud.com^d-2zejaz33icbp2vvvc9le podName: nodeName:}" failed. No retries permitted until 2020-11-05 14:38:12.653566679 +0800 CST m=+9150650.781033052 (durationBeforeRetry 2m2s). Error: "MountVolume.MountDevice failed for volume \"d-2zejaz33icbp2vvvc9le\" (UniqueName: \"kubernetes.io/csi/diskplugin.csi.alibabacloud.com^d-2zejaz33icbp2vvvc9le\") pod \"pod-e5ee2d454cdb4d1d916d933495e56cbe-3584893\" (UID: \"f8d71e90-d934-4d5a-b54f-62555da5df22\") : rpc error: code = Aborted desc = NodeStageVolume: Previous attach action is still in process
```

Cause:

You used an earlier version of CSI. Earlier versions of CSI use blkid to obtain uuid of the disk. The disks that are restored from snapshots share the same uuid, which causes the blkid command to stop.

Solution:

Restart the current node, or upgrade the CSI to the latest version.

## Why does the "FailedMount" warning appear when I start the pod?

Issue:

The following warning appears when you start the pod:

```
 Warning  FailedMount       104s                 kubelet, cn-zhangjiakou.172.20.11.162  Unable to attach or mount volumes: unmounted volumes=[sysdata-nas], unattached volumes=[kun-log kun-script kun-app sysdata-nas kun-patch default-token-rbx8p kun-etc kun-bin]: timed out waiting for the condition
 Warning  FailedMount       98s (x9 over 3m45s)  kubelet, cn-zhangjiakou.172.20.11.162  MountVolume.MountDevice failed for volume "nas-9d9ead08-8a1d-4463-a7e0-7bd0e3d3****" : kubernetes.io/csi: attacher.MountDevice failed to create newCsiDriverClient: driver name nasplugin.csi.alibabacloud.com not found in the list of registered CSI drivers
```

Cause:

The warning usually appears on newly created nodes. The system starts the CSI pods and the service pods at the same time, and it requires a certain time period to register CSI. Therefore, when you start to mount a volume to the service pod, the CSI registration may not be complete, which triggers the warning to appear.

Solution:

No operation is needed. The warning does not affect the startup of the pod.

## Why do I fail to delete a pod and Kubelet generates a pod log that is not managed by Container Service for Kubernetes \(ACK\)?

Issue:

You fail to delete a pod and Kubelet generates a pod log that is not managed by ACK.

Cause:

The pod exceptionally exits. Some mount targets are not removed when the system deletes the pod. As a result, the system fails to delete the pod. Kubelet cannot completely collect all volume garbage. You must remove invalid mount targets manually or by executing an automated script.

Solution:

Run the following script on the failed node to remove invalid mount targets:

```
wget https://raw.githubusercontent.com/AliyunContainerService/kubernetes-issues-solution/master/kubelet/kubelet.sh
sh kubelet.sh
```

## Why does the system prompt "chown: option not permitted" when I mount an Apsara File Storage NAS \(NAS\) file system?

Issue:

The system prompts "chown: option not permitted" when you mount a NAS file system.

Cause:

Your container does not have permissions to use the specified NAS file system.

Solution:

Launch the container with root privileges.

