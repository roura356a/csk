---
keyword: [disk volumes, usage notes]
---

# Usage notes for disk volumes

You can create volumes that mount Alibaba Cloud disks to a Container Service for Kubernetes \(ACK\) cluster. This topic describes the limits on and requirements of using disk volumes.

You can use the CSI plug-in provided by Alibaba Cloud to mount disks by creating persistent volumes \(PVs\) and persistent volume claims \(PVCs\). A PV can be statically or dynamically provisioned.

## Usage notes

-   A disk cannot be shared. If you use disks to create PVs or PVCs, you can mount a disk to only one pod.
-   We recommend that you use a StatefulSet to mount a disk. If you mount a disk by using a Deployment, you must set the number of replicated pods to 1. If the number of replicated pods is not set to 1, multiple nodes may share the disk when the pods are scheduled to different nodes. In this case, you cannot prioritize the node where you want to mount or unmount a disk. We recommend that you do not use this method to mount a disk.
-   Before you use a disk as a volume, create a disk and obtain its disk ID. For more information, see [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a disk.md).

    **Note:** Your disk must meet the following requirements:

    -   A basic disk must have a minimum capacity of 5 GiB.
    -   An ultra disk must have a minimum capacity of 20 GiB.
    -   A standard SSD must have a minimum capacity of 20 GiB.
    -   An enhanced SSD \(ESSD\) must have a minimum capacity of 20 GiB.
-   The node and the disk to be mounted must be in the same zone.
-   The type of disk and the type of Elastic Compute Service \(ECS\) instances that are used in your cluster must match before you can mount a disk. For more information about the matching rules between disks and ECS instances, see [Instance families](/intl.en-US/Instance/Instance families.md).
-   Only pay-as-you-go disks can be mounted. If you change the billing method of an Elastic Compute Service \(ECS\) instance in a cluster from pay-as-you-go to subscription, you cannot change the billing method of its disks to subscription. Otherwise, the disks cannot be mounted to the cluster.

