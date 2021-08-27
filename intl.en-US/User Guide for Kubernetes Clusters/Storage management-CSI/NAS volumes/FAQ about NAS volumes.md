---
keyword: [NAS volume, FAQ, frequently asked questions]
---

# FAQ about NAS volumes

This topic provides answers to some frequently asked questions about Apsara File Storage NAS \(NAS\) volumes.

-   [Why does the system prompt chown: option not permitted when I mount a NAS file system?](#section_srw_s3w_230)
-   [What do I do if the task queue of alicloud-nas-controller is full and PVs cannot be created when I mount a dynamically provisioned PV?](#section_gy3_aek_sdu)

## Why does the system prompt chown: option not permitted when I mount a NAS file system?

Symptom:

The system prompts chown: option not permitted when you mount a NAS file system.

Cause:

Your container does not have the permissions to use the specified NAS file system.

Solution:

Launch the container with root privileges.

## What do I do if the task queue of alicloud-nas-controller is full and PVs cannot be created when I mount a dynamically provisioned PV?

Symptom:

When you mount a NAS volume, the speed of subdirectory creations is faster than the speed of subdirectory deletions. This causes the task queue of alicloud-nas-controller to be blocked. As a result, new PVs cannot be created.

Cause:

The reclaimPolicy parameter is set to Delete and archiveOnDelete is set to false in the StorageClass that is used to mount the dynamically provisioned NAS volume.

Solution:

Set archiveOnDelete to true. This way, when a PV is deleted, only the name of the mounted subdirectory in the NAS file system is modified. The files in the subdirectory are not deleted.

You must manually delete these files. For example, you can enable the scheduled deletion mechanism for the root directory of the node, or start multiple pods to concurrently delete subdirectories of specific formats.

