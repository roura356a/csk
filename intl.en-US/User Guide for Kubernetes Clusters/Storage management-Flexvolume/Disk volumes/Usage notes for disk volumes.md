---
keyword: [disk volumes, statically provisioned volumes, dynamically provisioned volumes]
---

# Usage notes for disk volumes

This topic describes how to use Alibaba Cloud disks that are mounted as volumes in Container Service for Kubernetes \(ACK\) clusters.

## Background information

Alibaba Cloud disks can be mounted to ACK clusters in the following types:

-   Statically provisioned volumes

    You can use statically provisioned disk volumes in the following ways:

    -   Use disks as volumes.
    -   Use disks by creating persistent volumes \(PVs\) and persistent volume claims \(PVCs\).
-   Dynamically provisioned volumes

## Usage notes

-   A disk cannot be shared. If you use a disk by creating a pair of PV and PVC, the disk can be mounted to only one pod.
-   We recommend that you use a StatefulSet to mount a disk. To mount a disk by using a Deployment, you must set the number of replicated pods to 1. If you use a Deployment to mount a disk, you must set the number of replicated pods to 1. If the number of replicated pods is not set to 1, multiple nodes may share the disk when the pods are scheduled to different nodes. In this case, you cannot prioritize the node where you want to mount or unmount a disk. We recommend that you do not use this method to mount disks.
-   Before you can use a statically provisioned disk volume, you must create the disk and obtain the disk ID. For more information, see [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a disk.md).

    **Note:** Your disk must meet the following requirements:

    -   An ultra disk must have a minimum capacity of 20 GiB.
    -   A standard SSD must have a minimum capacity of 20 GiB.
    -   An enhanced SSD \(ESSD\) must have a minimum capacity of 20 GiB.
-   VolumeId: the ID of the disk that you want to mount. The value must be the same as the value of VolumeName and PV Name.
-   You can mount disks to only the nodes that are deployed in the same zone as the disks. Therefore, when you create a disk, select the zone of the pod to which you want to mount the disk.
-   Only pay-as-you-go disks can be mounted. If you change the billing method of an Elastic Compute Service \(ECS\) instance in the cluster from pay-as-you-go to subscription, you cannot change the billing method of its disks to subscription. Otherwise, the disks cannot be mounted to the cluster.
-   Some ECS instances do not support ESSDs. If this type of ECS instance is used as a node, you cannot mount an ESSD to the node. For more information, see [Elastic Block Storage FAQ](/intl.en-US/Block Storage/Elastic Block Storage FAQ.md).
-   If a disk is partitioned, you must re-initialize the disk before you mount it as a volume.

