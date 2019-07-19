# Create a serverless Kubernetes cluster {#task_e3c_311_ydb .task}

This topic describes how to use the Container Service console to create a serverless Kubernetes cluster.

[Container Service](https://partners-intl.console.aliyun.com/#/cs) and [RAM](https://partners-intl.console.aliyun.com/#/ram) must be activated.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Clusters**.
3.  In the upper-right corner, click **Create Kubernetes Cluster**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156350822951144_en-US.png)

4.  On the Select Cluster Template page, find the **Standard Serverless Cluster** template, and click **Create** in the template. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156350822951145_en-US.png)

5.  Enter the cluster name. 

    **Note:** The cluster name must be 1 to 63 characters in length and can contain letters, numbers, Chinese characters, letters, and hyphens \(-\).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156350822951146_en-US.png)

6.  Select the resource group for the cluster. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156350823049848_en-US.png)

7.  Select the region and zone where you want to locate the cluster. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156350823049849_en-US.png)

8.  Set a VPC for the serverless cluster. 

    **Note:** Kubernetes clusters support only the VPC network type.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156350823049850_en-US.png)

    To set a VPC for the serverless cluster, you can click one of the following two options:

    -   **Auto Create**: Set the system to automatically create a VPC. Furthermore, in the VPC, the system also creates an NAT gateway and sets an SNAT rule.
    -   **Use Existing**: Select an existing VPC from the VPC drop-down list, and then select a VSwitch from the VSwitch drop-down list.

        If you want to enable the cluster to access Internet resources, you must set an NAT gateway for the cluster. For more information, see [Create a NAT Gateway](../../../../reseller.en-US/User Guide/Manage a NAT Gateway/Create a NAT Gateway.md#).

9.  Set a NAT gateway and a SNAT rule for the VPC. 

    **Note:** 

    -   If you set the system to automatically create a VPC, you must select the **Nat Gateway** check box. This action sets the system to automatically set a NAT gateway and an SNAT rule for the VPC.
    -   If you select an existing VPC, you must manually set an NAT gateway or set an SNAT rule. Otherwise, the serverless cluster in the VPC cannot access the Internet, which results in a cluster creation failure.
10. Set the PrivateZone-based service discovery feature for the cluster. This feature allows you to use a domain name to access the corresponding service in the VPC. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156350823049851_en-US.png)

    **Note:** Before using this feature, you must activate the PrivateZone service, see [Service discovery based on Alibaba Cloud DNS PrivateZone](reseller.en-US/User Guide/Serverless Kubernetes cluster/Service discovery based on Alibaba Cloud DNS Private Zone.md#).

11. Attach a tag to the cluster. Enter a key and its value, and click **Add**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156350823049852_en-US.png)

12. Select the **Terms of Service for Serverless Kubernetes** check box.
13. In the upper-right corner, click **Create**

On the Cluster List page, view the serverless Kubernetes cluster you created.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6960/15635082305639_en-US.png)

In the **Action** column, click **Manage** to view the details of this cluster.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6960/15635082315640_en-US.png)

