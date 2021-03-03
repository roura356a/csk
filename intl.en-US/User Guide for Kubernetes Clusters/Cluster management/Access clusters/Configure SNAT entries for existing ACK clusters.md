# Configure SNAT entries for existing ACK clusters

You can enable Source Network Address Translation \(SNAT\) for a cluster of Alibaba Cloud Container Service for Kubernetes \(ACK\) when you create the cluster or enable this feature in the ACK console after the cluster is created. However, you cannot enable SNAT for existing ACK clusters by calling the API. This topic describes how to enable SNAT for existing ACK clusters in the ACK console. SNAT allows existing ACK clusters to access the Internet.

If an Elastic Compute Service \(ECS\) instance or an ACK cluster does not have a public IP address, you can create an SNAT entry in the Virtual Private Cloud \(VPC\) network where the ECS instance or ACK cluster is deployed to enable access to the Internet. For more information about SNAT, see [t13979.md\#](/intl.en-US/Product Introduction/What is NAT Gateway?.md).

## Procedure

1.  Log on to the [NAT Gateway console](https://vpc.console.aliyun.com/nat).

2.  In the left-side navigation pane, click **NAT Gateway**.

3.  On the **NAT Gateway** page, click **Create NAT Gateway** and complete the settings to create a Network Address Translation \(NAT\) gateway.

    For more information about the parameters required to create a NAT gateway, see [Create a NAT gateway]().

    **Note:** The NAT gateway and target ACK cluster must be deployed in the same region and VPC network.

4.  In the left-side navigation pane, choose **Elastic IP Addresses** \> **Elastic IP Addresses**. On the **Elastic IP Addresses** page, click **Create EIP**.

    If you have already purchased an elastic IP address \(EIP\) under the current account, skip this step.

5.  Bind the EIP to the NAT gateway.

    1.  On the **NAT Gateway** page, find the newly created NAT gateway, click the **![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2425674161/p140355.png)** icon in the Actions column, and then select **Bind Elastic IP Address** from the drop-down list.

    2.  On the **Bind Elastic IP Address** page, click the **Select from EIP List** tab. On the Select from EIP List tab, select the newly created EIP from the **Usable EIP List** drop-down list.

    3.  Click **OK**.

6.  Create an SNAT entry for the NAT gateway.

    1.  On the **NAT Gateway** page, find the newly created NAT gateway and click **Manage** in the Actions column.

    2.  In the left-side navigation pane, click **SNAT Table**.

    3.  On the **SNAT Table** page, click **Create SNAT Entry**.

        For more information about the parameters required to create an SNAT entry, see [Create a SNAT entry to access the Internet](/intl.en-US/User Guide/Create a SNAT entry to access the Internet.md).

    After the SNAT entry is created, SNAT is enabled for the target ACK cluster. You can log on to the [NAT Gateway console](https://vpc.console.aliyun.com/nat) to view the details about the NAT gateway, such as the EIP used by SNAT.

    ![NAT](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9435359951/p140370.png)


Run the following commands to log on to the cluster nodes and access the Internet to test the network connectivity and check the packet loss rate during data transmission.

![SNAT 2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9435359951/p140471.png)

