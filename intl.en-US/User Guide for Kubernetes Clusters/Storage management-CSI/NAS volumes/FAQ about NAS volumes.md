---
keyword: [NAS volume, FAQ, frequently asked questions]
---

# FAQ about NAS volumes

This topic provides answers to some frequently asked questions about Apsara File Storage NAS \(NAS\) volumes.

-   [Why does the system prompt chown: option not permitted when I mount a NAS file system?](#section_srw_s3w_230)
-   [What do I do if the task queue of alicloud-nas-controller is full and PVs cannot be created when I use a dynamically provisioned NAS volume?](#section_gy3_aek_sdu)
-   [Why does it require a long time to mount a NAS volume?](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/NAS volumes/FAQ about NAS volumes.md)

## Why does the system prompt chown: option not permitted when I mount a NAS file system?

Symptom:

The system prompts chown: option not permitted when you mount a NAS file system.

Cause:

Your container does not have permissions to use the specified NAS file system.

Solution:

Launch the container with root privileges.

## What do I do if the task queue of alicloud-nas-controller is full and PVs cannot be created when I use a dynamically provisioned NAS volume?

Symptom:

When you use a dynamically provisioned NAS volume, if the speed of subdirectory creation is faster than the speed of subdirectory deletion, the task queue of alicloud-nas-controller may be full and therefore PVs cannot be created.

Cause:

The reclaimPolicy parameter is set to Delete and the archiveOnDelete parameter is set to false in the configuration of the StorageClass that mounts the dynamically provisioned NAS volume.

Solution:

Set archiveOnDelete to true. This way, when a PV is deleted, only the name of the mounted subdirectory in the NAS file system is modified. The files in the subdirectory are not deleted.

You must delete these files yourself. For example, you can configure a node to automatically delete files in the root directory by schedule, or start multiple pods to concurrently delete files of specific formats in subdirectories.

## Why does it require a long time to mount a NAS volume?

Symptom:

It requires a long time to mount a NAS volume.

Cause:

-   If you set the mode parameter in the PV template and the mounted directory stores a large number of files, the mount process may require an excessive amount of time or even fail.
-   If the securityContext.fsgroup parameter is set in the application template, kubelet performs the `chmod` or `chown` operation after the volume is mounted, which increases the time consumption.

Solution:

-   Delete the mode parameter in the PV template.
-   If the securityContext.fsgroup parameter is set in the application template, delete the fsgroup parameter in the securityContext section.

