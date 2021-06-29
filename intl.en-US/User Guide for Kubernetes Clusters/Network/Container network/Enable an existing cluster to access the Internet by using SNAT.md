---
keyword: [SNAT, Internet access, clusters]
---

# Enable an existing cluster to access the Internet by using SNAT

If an Elastic Compute Service \(ECS\) instance or an ACK cluster does not have a public IP address, you can create an SNAT entry in the virtual private cloud \(VPC\) where the ECS instance or ACK cluster is deployed to provide a proxy to enable access to the Internet. If Source Network Address Translation \(SNAT\) is not enabled when you create a Container Service for Kubernetes \(ACK\) cluster, you can enable SNAT in the ACK console after the cluster is created. This topic describes how to enable SNAT for existing ACK clusters in the ACK console. SNAT allows existing ACK clusters to access the Internet.

You cannot call API operations to enable SNAT for existing clusters. For more information about SNAT, see [What is NAT Gateway?](/intl.en-US/Product Introduction/What is NAT Gateway?.md).

## Procedure

The following flowchart shows the steps to enable SNAT for an existing ACK cluster to access the Internet.

![Enable SNAT](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3036270261/p259133.png)

1.  Create a NAT gateway.

    1.  Log on to the [NAT Gateway console](https://vpc.console.aliyun.com/nat).

    2.  In the left-side navigation pane, click **NAT Gateway**.

    3.  On the **NAT Gateway** page, click **Create NAT Gateway**.

        For more information about the parameters required to create a NAT gateway, see [Create NAT gateways](/intl.en-US/User Guide/Create NAT gateways.md).

        **Note:** The NAT gateway must be created in the same region and VPC as the ACK cluster.

2.  Create an EIP.

    In the left-side navigation pane, choose **Elastic IP Addresses** \> **Elastic IP Addresses**. On the **Elastic IP Addresses** page, click **Create EIP**.

    If you have an elastic IP address \(EIP\), skip this step.

3.  Associate the EIP with the created NAT gateway.

    1.  On the **NAT Gateway** page, find the newly created NAT gateway and choose **![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2425674161/p140355.png)** \> **Bind Elastic IP Address** in the Actions column.

    2.  In the **Associate EIP** dialog box, select a resource group from the **Resource Group** drop-down list and select the EIP that you created from the **Select Existing EIPs** drop-down list.

    3.  Click **OK**.

4.  Create an SNAT entry for the NAT gateway.

    1.  On the **NAT Gateway** page, find the newly created NAT gateway and click **Manage** in the Actions column.

    2.  On the **SNAT Management** tab, click **Create SNAT Entry**.

    3.  On the **Create SNAT Entry** page, set the parameters as described in the following table and click **Confirm**.

        For more information about the parameters, see [Create a SNAT entry](/intl.en-US/User Guide/Configure SNAT to access the Internet.md).

        |Parameter|Description|
        |---------|-----------|
        |**SNAT Entry**|Select **Specify VSwitch** and select the vSwitches that are used by the cluster.         -   If the cluster uses Terway as the network plug-in, select the node vSwitches and pod vSwitches.
        -   If the cluster uses Flannel as the network plug-in, select the node vSwitches.
Perform the following steps to check the IDs of the vSwitches used by the cluster:

        1.  Log on to the [ACK console](https://cs.console.aliyun.com).
        2.  In the left-side navigation pane, click **Clusters**. On the Clusters page, find the cluster that you want to manage and click its name or click **Details** in the **Actions** column.
        3.  On the details page of the cluster, click the **Cluster Resources** tab to view the vSwitch IDs.

![Clusters that use Flannel](../images/p255169.png "Clusters that use Flannel")

![Clusters that use Terway](../images/p255209.png "Clusters that use Terway") |
        |**Select Public IP Address**|Select a public IP address that is used to access the Internet.|

    After the SNAT entry is created and SNAT rules are configured, SNAT is enabled for the cluster. You can log on to the [NAT Gateway console](https://vpc.console.aliyun.com/nat) to view the details of the NAT gateway, such as the EIP used by SNAT. The following figure shows a NAT gateway that is used for an ACK cluster that uses Terway as the network plug-in. This NAT gateway is configured with SNAT rules to enable the cluster to access the Internet.

    ![NAT](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2426537161/p140370.png)

    Click the name of the NAT gateway. On the **SNAT Management** tab of the details page, you can check whether the public IP address is associated with the vSwitches used by the cluster. The following figure shows the SNAT entries created for the cluster that uses Terway as the network plug-in.

    ![snat](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3036270261/p259134.png)


Log on to a node of the cluster and access the Internet to verify that the node can access the Internet and no packet loss occurs during data transmission.

![SNAT 2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5028162261/p140471.png)

