# Use Alibaba Cloud disks as volumes

You can use volumes created from Alibaba Cloud disks in Kubernetes clusters.

Currently, Alibaba Cloud disks can be attached to Kubernetes clusters as follows:

-   Static volumes

    You can use a static volume by creating a PV and a PVC.

-   Dynamic volumes

## Note

-   A disk is a non-shared storage that can only be mounted to one Pod at one time.
-   We recommend that you attach a disk to a StatefulSet. If you attach a disk to a deployment, you must set the number of replicas to 1. If the number of replicas is not set to 1, multiple nodes use the disk at the same time. An error may occur because a disk can be attached to only one node at a time.
-   Disks can only be mounted to nodes that are located in the same zone.
-   To use a disk as a volume, you must create the disk and obtain its disk ID first. [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a pay-as-you-go disk.md).

    **Note:** Your disk must meet the following capacity requirements:

    -   A basic disk must have a minimum capacity of 5 GiB.
    -   An ultra disk must have a minimum capacity of 20 GiB.
    -   An SSD disk must have a minimum capacity of 20 GiB.
    -   An ESSD disk must have a minimum capacity of 20 GiB.
-   Only pay-as-you-go disks can be mounted. If you change the billing method of an ECS instance in the cluster from pay-as-you-go to subscription, you cannot change the billing method of its disks to subscription. Otherwise, the disks cannot be mounted to the cluster.

