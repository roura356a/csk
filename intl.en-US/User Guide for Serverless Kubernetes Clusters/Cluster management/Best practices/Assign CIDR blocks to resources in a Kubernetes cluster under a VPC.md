# Assign CIDR blocks to resources in a Kubernetes cluster under a VPC

When you create a Kubernetes cluster, you must specify a VPC, VSwitches, CIDR blocks of pods, and CIDR blocks of services. Therefore, we recommend that you plan the IP address of each Elastic Compute Service \(ECS\) instance in the cluster, the CIDR block of pods, and the CIDR block of services before you create a Kubernetes cluster. This topic describes the functions of CIDR blocks that are assigned to resources in a Kubernetes cluster under an Alibaba Cloud VPC and how to plan the CIDR blocks.

## VPC-related CIDR blocks and Kubernetes cluster-related CIDR blocks

VPC-related CIDR blocks include the CIDR block of the VPC and CIDR blocks of the VSwitches in the VPC. Kubernetes cluster-related CIDR blocks include the CIDR block of pods and that of services. The following figure shows the relationship between VPC-related CIDR blocks and Kubernetes cluster-related CIDR blocks:

![The relationship between Kubernetes cluster-related CIDR blocks and VPC-related CIDR blocks](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4635359951/p8764.png)

The following list describes the CIDR blocks:

-   The CIDR block of the VPC.

    The CIDR block that you select when you create a VPC. You can select one of the following CIDR blocks: 10.0.0.0/8, 172.16.0.0/12, and 192.168.0.0/16.

-   The CIDR blocks of the VSwitches.

    The CIDR blocks that you specify when you create VSwitches in the VPC. These CIDR blocks must be subsets of the VPC CIDR block. The VPC CIDR block is considered a subset of itself.

    -   The IP addresses of the ECS instances under a VSwitch belong to the CIDR block of the VSwitch.
    -   Multiple VSwitches can be created under one VPC. The VSwitch CIDR blocks cannot overlap with each other.
-   The CIDR block of pods in the cluster.

    A pod is a concept in Kubernetes. Each pod has one IP address.

    -   The pod CIDR block cannot overlap with the VPC CIDR block.
    -   The pod CIDR block cannot overlap with the service CIDR block.
    For example, if the VPC CIDR block is 172.16.0.0/12, the pod CIDR block cannot be 172.16.0.0/16 or 172.17.0.0/16, because these CIDR blocks are subsets of 172.16.0.0/12.

-   The CIDR block of services.

    Service is a concept in Kubernetes. A service has an IP address if the `Type` attribute is set to ClusterIP.

    -   The IP address of a service applies only within the Kubernetes cluster.
    -   The service CIDR block cannot overlap with the VPC CIDR block.
    -   The service CIDR block cannot overlap with the pod CIDR block.

## Select CIDR blocks

-   **Scenario 1: one VPC and one Kubernetes cluster**

    This is the simplest scenario. The CIDR block of a VPC is specified when you create the VPC. Make sure that the CIDR block of pods and that of services are different from the VPC CIDR block.

-   **Scenario 2: one VPC and multiple Kubernetes clusters**

    Assume that multiple Kubernetes clusters are created under a VPC.

    -   The VPC CIDR block is specified when you create the VPC. Make sure that the VPC CIDR block, service CIDR block, and pod CIDR block of each cluster do not overlap with each other.
    -   The pod CIDR blocks of Kubernetes clusters cannot overlap with each other. The service CIDR blocks of Kubernetes clusters can overlap with each other.
    -   In the default network mode \(Flannel\), the packets of pods must be forwarded by the VPC router. ACK automatically generates a route table for each destination pod CIDR block on the VPC router.
    **Note:** In this case, a pod in one cluster can connect to the pods and ECS instances in another cluster, but cannot connect to the services in another cluster.

-   **Scenario 3: two connected VPCs**

    If two VPCs are connected, you can use the route table of a VPC to configure which packets of the VPC are to be sent to the peer VPC. Take the following scenario as an example: VPC 1 uses the CIDR block 192.168.0.0/16 and VPC 2 uses the CIDR block 172.16.0.0/12. You can use the route table to specify VPC 2 as the destination of packets that use 172.16.0.0/12 as the destination CIDR block.

    ![Route table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4635359951/p8765.png)

    |VPC|CIDR block|Destination CIDR block|Destination VPC|
    |---|----------|----------------------|---------------|
    |VPC 1|192.168.0.0/16|172.16.0.0/12|VPC 2|
    |VPC 2|172.16.0.0/12|192.168.0.0/16|VPC 1|

    In this scenario, the CIDR block of a Kubernetes cluster under VPC 1 is subject to the following limits:

    -   The CIDR block cannot overlap with the CIDR block of VPC 1.
    -   The CIDR block cannot overlap with the CIDR block of VPC 2.
    -   The CIDR block cannot overlap with those of other clusters.
    -   The CIDR block cannot overlap with the CIDR blocks of pods in the cluster.
    -   The CIDR block cannot overlap with the CIDR blocks of services in the cluster.
    The CIDR block of a Kubernetes cluster under VPC 2 is subject to similar limits.

    In this example, you can set the CIDR block of pods in a Kubernetes cluster to a subset of the 10.0.0.0/8 CIDR block.

    **Note:** The CIDR blocks of a Kubernetes cluster cannot overlap with CIDR blocks that are routed to VPC 2.

    To access a pod in a Kubernetes cluster under VPC 1 from VPC 2, configure a route from VPC 2 to the pod CIDR block of the destination cluster in VPC 1.

-   **Scenario 4: a VPC connected to a data center**

    If a VPC is connected to a data center, some VPC CIDR blocks are routed to the data center. In this case, the pod CIDR blocks of Kubernetes clusters under the VPC cannot overlap with these CIDR blocks. To access pods in a Kubernetes cluster under the VPC from the data center, configure the routes from the data center to the leased line-based virtual border router \(VBR\).


