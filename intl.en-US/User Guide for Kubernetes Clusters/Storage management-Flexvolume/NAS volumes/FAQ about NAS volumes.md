---
keyword: [NAS volume, FAQ, frequently asked questions, FlexVolume]
---

# FAQ about NAS volumes

This topic provides answers to some frequently asked questions about Apsara File Storage NAS \(NAS\) volumes.

-   [Why does it require a long time to mount a NAS volume?](#section_td0_7vk_92o)
-   [Why does a timeout error occur when I mount a NAS volume?](#section_n33_3bc_3jb)
-   [Why does the system prompt chown: option not permitted when I mount a NAS volume?](#section_vxc_7oy_i24)
-   [What do I do if I fail to mount a NAS volume?](#section_f1w_yob_goz)
-   [What do I do if the task queue of alicloud-nas-controller is full and PVs cannot be created when I use a dynamically provisioned NAS volume?](#section_xgz_7fw_p7e)

## Why does it require a long time to mount a NAS volume?

Symptom:

It requires a long time to mount a NAS volume.

Cause:

-   If you set the mode parameter in the PV template and the mounted directory stores a large number of files, the mount process may require an excessive amount of time or even fail.
-   If the securityContext.fsgroup parameter is set in the application template, kubelet performs the `chmod` or `chown` operation after the volume is mounted, which increases the time consumption.

Solution:

-   Delete the mode parameter in the PV template.
-   If the securityContext.fsgroup parameter is set in the application template, delete the fsgroup parameter in the securityContext section.

## Why does a timeout error occur when I mount a NAS volume?

Symptom:

A timeout error occurred when you mount a NAS volume.

Cause:

The mount target of the NAS file system and the cluster are not in the same virtual private cloud \(VPC\).

Solution:

Select a NAS file system whose mount target is in the same VPC as the cluster.

## Why does the system prompt chown: option not permitted when I mount a NAS volume?

Symptom:

The system prompts chown: option not permitted when you mount a NAS volume.

Cause:

Your container does not have permissions to use the specified NAS file system.

Solution:

Launch the container with root privileges.

## What do I do if I fail to mount a NAS volume?

Symptom:

Your attempt to mount a NAS persistent volume \(PV\) failed and the system prompts the following error:

```
Unable to mount volumes for pod "dp-earnings-pod_default(906172c6-3d68-11e8-86e0-00163e00****)": timeout expired waiting for volumes to attach/mount for pod "default"/"dp-earnings-pod". list of unattached/unmounted volumes=[vol1 vol2]
```

Cause:

The FlexVolume plug-in is not installed.

Solution:

Install the FlexVolume plug-in. For more information, see [Install and upgrade FlexVolume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Install and upgrade FlexVolume.md).

## What do I do if the task queue of alicloud-nas-controller is full and PVs cannot be created when I use a dynamically provisioned NAS volume?

Symptom:

When you use a dynamically provisioned NAS volume, if the speed of subdirectory creation is faster than the speed of subdirectory deletion, the task queue of alicloud-nas-controller may be full and therefore PVs cannot be created.

Cause:

The reclaimPolicy parameter is set to Delete and the archiveOnDelete parameter is set to false in the configuration of the StorageClass that mounts the dynamically provisioned NAS volume.

Solution:

Set archiveOnDelete to true. This way, when a PV is deleted, only the name of the mounted subdirectory in the NAS file system is modified. The files in the subdirectory are not deleted.

You must delete these files yourself. For example, you can configure a node to automatically delete files in the root directory by schedule, or start multiple pods to concurrently delete files of specific formats in subdirectories.

