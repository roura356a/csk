---
keyword: [secondary CIDR block, vSwitch, Kubernetes cluster]
---

# Add a vSwitch to a cluster based on a secondary CIDR block

When you create a cluster in Container Service for Kubernetes \(ACK\), you must specify a virtual private cloud \(VPC\) for the cluster. If you want to expand the cluster, you must deploy cloud resources in the VPC to which the cluster belongs. If the CIDR block of the VPC does not have sufficient IP addresses, you can add a secondary CIDR block to the VPC. This way, you can expand the cluster based on your business requirements. This topic describes how to add a vSwitch to a cluster based on a secondary CIDR block.

-   The CIDR block of the VPC to which the cluster belongs does not have sufficient IP addresses.
-   A managed ACK cluster is created in February 2021 or later or a dedicated ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md) or [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).

    **Note:** You cannot add a secondary CIDR block to the VPC if your managed ACK cluster was created earlier than February 2021. In this case, you can migrate workloads to a professional managed cluster and then add a vSwitch to the cluster based on a secondary CIDR block. For more information about how to migrate workloads from standard managed clusters to professional managed clusters, see [Hot migration from standard managed Kubernetes clusters to professional managed Kubernetes clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Hot migration from standard managed Kubernetes clusters to professional managed Kubernetes clusters.md).


## Procedure

1.  Select an available CIDR block.

    1.  Check the CIDR blocks that are in use.

        The CIDR blocks include but are not limited to:

        -   The current CIDR block of the VPC.

            For more information about how to check the current CIDR block of a VPC, see [View a VPC](/intl.en-US/VPCs and vSwitchs/Work with VPCs.md).

            ![VPC CIDR ](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3848823261/p271329.png)

        -   The CIDR blocks of the pods and Services that are deployed in the VPC.

            For more information about how to check the CIDR blocks of pods and Services, see [View basic information](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/View cluster information.md).

            **Note:**

            -   If the cluster uses the Terway network plug-in, check the CIDR block of Services.
            -   If the cluster uses the Flannel network plug-in, check the CIDR blocks of pods and Services.
        -   The CIDR blocks of connections over Express Connect circuits, VPN gateways, and Cloud Enterprise Network \(CEN\) instances that are connected to the VPC.
    2.  Select a CIDR block that does not overlap with the preceding CIDR blocks, and use this CIDR block as the secondary CIDR block of the VPC.

        For example, a cluster that uses the Flannel network plug-in may use the following CIDR blocks:

        -   VPC CIDR block: 192.168.0.0/16
        -   Pod CIDR block: 172.20.0.0/16
        -   Service CIDR block: 172.21.0.0/16
        -   The VPC is not connected with connections over Express Connect circuits, VPN gateways, or Cloud Enterprise Network \(CEN\) instances.
        In this case, you can use 10.0.0.0/8 as a secondary CIDR block.

2.  Add a secondary CIDR block and vSwitch in the VPC console.

    1.  Log on to the [VPC console](https://vpcnext.console.aliyun.com/vpc).

    2.  In the top navigation bar, select the region where the VPC is deployed.

    3.  On the **VPC** page, find the VPC that you want to manage and click its ID.

    4.  On the **VPC Details** page, click the **CIDRs** tab. Then, click **Add IPv4 CIDR** and enter the IPv4 CIDR block that you selected in the previous step.

    5.  You can create a vSwitch in the secondary CIDR block based on your business requirements. The zone of the vSwitch must be the same as the zone of the secondary CIDR block.

        For more information about how to create a vSwitch, see [Create a vSwitch](/intl.en-US/VPCs and vSwitchs/Work with vSwitches.md).

3.  Add rules to the security group of the cluster to allow inbound and outbound access to the secondary CIDR block over all protocols.

    For more information about how to add security group rules, see [Add security group rules](/intl.en-US/Security/Security groups/Add security group rules.md).

4.  Expand the cluster by creating nodes in the new vSwitch.

    When you expand the cluster, you must deploy new nodes in the new vSwitch. The CIDR block of the new vSwitch belongs to the secondary CIDR block of the VPC. For more information, see [Expand an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Expand an ACK cluster.md).

    **Note:** If the cluster uses the Terway network plug-in, you can modify the vSwitch settings of the Terway plug-in to provide more IP addresses for pods. For more information about how to enable pods to use the IP addresses provided by the new vSwitch, see [Create vSwitches for an ACK cluster that has Terway installed](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Create vSwitches for an ACK cluster that has Terway installed.md).

    After new nodes are deployed in the new vSwitch,

    -   the master nodes and new nodes can directly communicate with each other in dedicated ACK clusters. No extra configuration is required.
    -   extra configuration is required on the control planes of standard managed clusters and professional managed clusters. Otherwise, you cannot use the API server to run kubectl exec or kubectl logs commands on the pods of new nodes. You must [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to ask technical support staff to reconfigure the control plane.
5.  Verify that the secondary CIDR block of the VPC works as expected.

    Perform the following steps:

    -   Verify that the IP addresses of new nodes and the IP addresses of the pods hosted by new nodes belong to the secondary CIDR block of the VPC.
    -   Verify that the new nodes are in the **Ready** state.
    -   Verify that the new nodes can communicate with the existing nodes in the cluster and that the pods hosted on new nodes can communicate with the pods hosted on existing nodes.

**Related topics**  


[Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md)

[Add a secondary IPv4 CIDR block](/intl.en-US/VPCs and vSwitchs/Work with VPCs.md)

[Create vSwitches for an ACK cluster that has Terway installed](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Create vSwitches for an ACK cluster that has Terway installed.md)

[Hot migration from standard managed Kubernetes clusters to professional managed Kubernetes clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Hot migration from standard managed Kubernetes clusters to professional managed Kubernetes clusters.md)

