---
keyword: [use Alibaba Cloud disks as volumes, static volumes, dynamic volumes]
---

# Use Alibaba Cloud disks as volumes

This topic describes how to mount Alibaba Cloud disks as volumes to Container Service for Kubernetes \(ACK\) clusters.

## Background information

Alibaba Cloud disks can be mounted to ACK clusters in the following types:

-   Static volumes

    You can use static volumes in the following ways:

    -   Use disks as volumes.
    -   Use disks to create persistent volumes \(PVs\) or persistent volume claims \(PVCs\).
-   Dynamic volumes

## Considerations

-   A disk cannot be shared. If you use disks to create PVs or PVCs, you can mount a disk to one pod only.
-   We recommend that you mount disks to StatefulSets. To mount disks to deployments, set the number of pod replicas to 1.
-   Before you use a disk as a volume, create a disk and obtain its disk ID. For more information, see [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a pay-as-you-go disk.md).

    **Note:** Your disk must meet the following requirements:

    -   A basic disk must have a minimum capacity of 5 GiB.
    -   An ultra disk must have a minimum capacity of 20 GiB.
    -   An SSD must have a minimum capacity of 20 GiB.
    -   An enhanced SSD \(ESSD\) must have a minimum capacity of 20 GiB.
-   The volume ID must be the same as the ID of the mounted disk. The volume name must be the same as the PV name.
-   The node and the disk to be mounted must be in the same zone.
-   Only pay-as-you-go disks can be mounted. If you change the billing method of an Elastic Compute Service \(ECS\) instance in a cluster from pay-as-you-go to subscription, you cannot change the billing method of its disks to subscription. Otherwise, the disks cannot be mounted to the cluster.
-   Some ECS instances do not support ESSDs. If nodes on ECS instances do not support ESSDs, you cannot mount ESSDs to the ECS instances. For more information, see [Block Storage FAQ](/intl.en-US/Block Storage/Block Storage FAQ.md).
-   If a disk is partitioned, before you use the disk as a volume, initialize the disk again.

