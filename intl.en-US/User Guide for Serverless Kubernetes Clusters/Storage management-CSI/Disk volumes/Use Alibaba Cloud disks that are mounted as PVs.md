# Use Alibaba Cloud disks that are mounted as PVs

This topic describes how to use cloud disks of Alibaba Cloud that are mounted persistent volumes \(PVs\) in clusters of Container Service for Kubernetes \(ACK\).

You can use the CSI plug-in provided by Alibaba Cloud to mount cloud disks as static and dynamic PVs through persistent volume claims \(PVCs\).

## Notes

-   A disk cannot be shared. If you use disks to create PVs or PVCs, you can mount a disk to only one pod.
-   We recommend that you use a StatefulSet to mount a cloud disk as a PV. If you use a Deployment to mount a cloud disk, you must set the number of pod replicas to 1. If the number of pod replicas is not set to 1, multiple nodes may use the disk at the same time when pods replicas are scheduled to different nodes. In this case, you cannot prioritize the node where you want to mount or unmount a cloud disk.
-   Before you use a disk as a volume, create a disk and obtain its disk ID. For more information, see [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a disk.md).

    **Note:** Your disk must meet the following requirements:

    -   A basic disk must have a minimum capacity of 5 GiB.
    -   An ultra disk must have a minimum capacity of 20 GiB.
    -   A standard SSD must have a minimum capacity of 20 GiB.
    -   An enhanced SSD \(ESSD\) must have a minimum capacity of 20 GiB.
-   The node and the disk to be mounted must be in the same zone.
-   Only pay-as-you-go disks can be mounted. If you change the billing method of an Elastic Compute Service \(ECS\) instance in a cluster from pay-as-you-go to subscription, you cannot change the billing method of its disks to subscription. Otherwise, the disks cannot be mounted to the cluster.

