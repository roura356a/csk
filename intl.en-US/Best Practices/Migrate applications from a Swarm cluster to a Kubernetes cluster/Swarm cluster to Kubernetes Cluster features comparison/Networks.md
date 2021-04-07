# Networks

This topic describes the differences in the networks supported by Container Service for Swarm clusters and Container Service for Kubernetes \(ACK\) clusters.

## Swarm cluster

Swarm clusters support the following network types:

-   Virtual private cloud \(VPC\)
-   Classic network

## ACK cluster

ACK clusters support only VPCs. For more information, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md).

-   If a Swarm cluster is deployed in a VPC, you must select the same VPC when you create an ACK cluster for migration. This ensures that the Swarm cluster and ACK cluster can communicate with each other.
-   If a Swarm cluster is deployed in the classic network, you must first connect the classic network to the VPC where you want to deploy the ACK cluster. This is because ACK clusters support only VPCs. For more information, see [Overview](/intl.en-US/Best practices/Classic network-to-VPC migration/Overview.md).

    After the classic network is connected to the VPC, IP addresses for access from VPCs are generated for Alibaba Cloud services that are used by the Swarm cluster, such as Object Storage Service \(OSS\), Apsara File Storage NAS \(NAS\), and Relational Database Service \(RDS\). The created ACK cluster can connect to the IP addresses to access these Alibaba Cloud services through the VPC where the ACK cluster is deployed.


