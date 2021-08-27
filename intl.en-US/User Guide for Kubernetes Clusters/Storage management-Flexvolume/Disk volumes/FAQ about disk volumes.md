---
keyword: [disk volumes, FAQ, frequently asked questions, FlexVolume]
---

# FAQ about disk volumes

This topic provides answers to some frequently asked questions about disk volumes.

-   [Why does a timeout error occur when I mount a disk to a node?](#section_td0_7vk_92o)
-   [Why does an input/output error occur in a disk after a system upgrade?](#section_vxc_7oy_i24)
-   [Why does the system prompt The specified disk is not a portable disk when I unmount a disk?](#section_f1w_yob_goz)
-   [Why does the system prompt had volume node affinity conflict when I launch a pod that has a disk mounted?](#section_fay_4ao_0ik)
-   [Why does the system prompt can't find disk when I launch a pod that has a disk mounted?](#section_kwt_2d5_r0u)
-   [Why does the system prompt disk size is not supported when I dynamically provision a PV?](#section_vi0_dfh_v7w)

## Why does a timeout error occur when I mount a disk to a node?

Symptom:

A timeout error occurs when you mount a disk to a node.

Cause:

If the node is manually added to the cluster, the cause may be that the node does not have Security Token Service \(STS\) permissions.

Solution:

Manually assign a Resource Access Management \(RAM\) role to the instance. For more information, see [Bind an instance RAM role](/intl.en-US/Security/Manage identities and permissions/Use RAM roles to control resource access/Bind an instance RAM role.md).

## Why does a zone error occur when I mount a disk to an Elastic Compute Service \(ECS\) instance?

Symptom:

A zone error occurred when you mount a disk to an ECS instance.

Cause:

The disk and the ECS instance are not deployed in the same zone in the same region.

Solution:

To mount a disk to an ECS instance, the disk and the ECS instance must be deployed in the same zone in the same region.

## Why does an input/output error occur in a disk after a system upgrade?

Symptom:

An input/output error occurs in a disk after a system upgrade.

Cause:

The FlexVolume version is outdated.

Solution:

1.  Upgrade FlexVolume to V1.9.7-42e8198 or later.

    ```
    kubectl set image daemonset/flexvolume acs-flexvolume=registry.cn-hangzhou.aliyuncs.com/acs/flexvolume:v1.9.7-42e8198 -n kube-system
    ```

2.  Recreate the pod that has the error.

For more information about FlexVolume versions, see [FlexVolume](/intl.en-US/Release notes/System Component change Records/Storage/FlexVolume.md).

## Why does the system prompt The specified disk is not a portable disk when I unmount a disk?

Symptom:

The system prompts The specified disk is not a portable disk when you unmount a disk.

Cause:

The disk is billed on a subscription basis, or you accidentally switched the billing method of the disk to subscription when you upgraded the ECS instance that is associated with the disk.

Solution:

Switch the billing method of the disk from subscription to pay-as-you-go.

## Why does the system prompt had volume node affinity conflict when I launch a pod that has a disk mounted?

Symptom:

Your attempt to launch a pod that has a disk mounted and failed the system prompts had volume node affinity conflict.

Cause:

You set the `nodeaffinity` attribute in the persistent volume \(PV\) configurations to a value different from that in the pod configurations. Therefore, the pod cannot be scheduled to an appropriate node.

Solution:

Modify the nodeaffinity attribute of the PV or pod to ensure that the PV and pod use the same value.

## Why does the system prompt can't find disk when I launch a pod that has a disk mounted?

Symptom:

The system prompts The specified disk is not a portable disk when you unmount a disk.

Cause:

-   You entered an invalid value for the DiskID parameter when you configure the PV.
-   Your account does not have the permissions to modify the DiskID parameter. The disk that you specified may not belong to the current account.

Solution:

Modify the DiskID parameter.

## Why does the system prompt disk size is not supported when I dynamically provision a PV?

Symptom:

Your attempt to dynamically provision a PV failed and the system prompts disk size is not supported.

Cause:

The size of the disk that you specified in the persistent volume claim \(PVC\) is invalid. The disk size must be at least 20 GiB.

Solution:

Change the size of the disk that is specified in the PVC to a valid value.

