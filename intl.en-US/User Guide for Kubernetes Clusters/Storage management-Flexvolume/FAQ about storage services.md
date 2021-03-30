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
-   [Why do I fail to delete a pod and Kubelet generates a pod log that is not managed by Container Service for Kubernetes \(ACK\)?](#section_u64_r44_iej)

**FAQ about NAS storage**

-   [Why does the system prompt "chown: option not permitted" when I mount an Apsara File Storage NAS \(NAS\) file system?](#section_cxn_2gp_hxw)
-   [Why do I fail to mount a NAS PV?](#section_1h3_9u3_zox)

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

## Why do I fail to mount a NAS PV?

Issue:

You fail to mount a NAS PV and the system prompts the following error:

```
Unable to mount volumes for pod "dp-earnings-pod_default(906172c6-3d68-11e8-86e0-00163e0084d3)": timeout expired waiting for volumes to attach/mount for pod "default"/"dp-earnings-pod". list of unattached/unmounted volumes=[vol1 vol2]
```

Cause:

The Flexvolume plug-in is not installed.

Solution:

Install the Flexvolume plug-in. For more information, see [Install plug-ins](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Install plug-ins.md).

