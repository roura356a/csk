# Fixes the issue where data disks fail to be mounted to a multi-zone ACK cluster

Alibaba Cloud has fixed the issue where data disks cannot be mounted to a multi-zone Container Service for Kubernetes \(ACK\) cluster. This issue is fixed for newly created multi-zone ACK clusters. In existing multi-zone ACK clusters, if the number of running applications or pulled images increases, the disk space may become insufficient on nodes where the Docker data directory is not mounted with data disks. ACK provides a solution to fix this issue. For more information, see [t119756.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Attach a data disk to a node.md). If you have further questions or require assistance, contact the ACK support team on DingTalk.

