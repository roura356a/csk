# Failed to create a Kubernetes cluster {#concept_l1t_3vb_wdb .concept}

## Check the cause of failure {#section_mct_jvb_wdb .section}

You can check the cause of cluster creation failure by viewing the cluster creation events.

Log on to the [Resource Orchestration Service \(ROS\) console](https://parnters-intl.console.aliyun.com/#/ros).

Select the region where the cluster resides. Then, click **Manage** at the right of the cluster. In the left-side navigation pane, click **Event**. Rest the pointer on the failed event to view the specific error message of the failure.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15844/15440798029797_en-US.png)

If the error message in the figure is displayed, it means that the cluster creation failed because the number of Virtual Private Cloud \(VPC\) instances has reached the quota.

## Failure codes and solutions {#section_dtt_1wb_wdb .section}

-   Resource CREATE failed: ResponseException: resources.k8s\_SNat\_Eip: Elastic IP address quota exceeded Code: QuotaExceeded.Eip

    **Solution:** Release unused EIPs, or open a ticket to raise the EIP quota.

-   Resource CREATE failed: ResponseException: resources.k8s\_master\_slb\_internet: The maximum number of SLB instances is exceeded. Code: ORDER.QUANTITY\_INVALID

    **Solution:** Release unused SLB instances, or open a ticket to raise the SLB quota.

-   Resource CREATE failed: ResponseException: resources.k8s\_vpc: VPC quota exceeded. Code: QuotaExceeded.Vpc

    **Solution:** Release unused VPCs, or open a ticket to raise the VPC quota.

-   Status Code: 403 Code: InvalidResourceType.NotSupported Message: This resource type is not supported;

    **Solution:** No ECS in stock or the type is not supported. Select other ECS specifications and try again.

-   Resource CREATE failed: ResponseException: resources.k8s\_master\_1: The specified image does not support cloud-init. Code: ImageNotSupportCloudInit

    **Solution:** When a custom image is used to create a cluster, the custom image must be based on the latest CentOS image.

-   Resource CREATE failed: ResponseException: resources.k8s\_nodes: The resource is out of stock in the specified zone. Please try other types, or choose other regions and zones. Code: OperationDenied.NoStock

    **Solution:** The instances of your selected specifications are sold out. Select other availability zones or specifications, and try again.

-   Resource CREATE failed: ResponseException: resources.k8s\_NAT\_Gateway: A route entry already exists, which CIDR is '0.0.0.0/0' Code: RouterEntryConflict.Duplicated

    **Solution:** Current route table of the VPC includes system route entries. Delete the system route entries, or clear the Configure SNAT for VPC check box, and try again.

-   Resource CREATE failed: ResponseException: resources.KubernetesWorkerRole: The number of role is limited to 200. Code: LimitExceeded.Role

    **Solution:** The number of RAM roles has reached the quota. Delete some RAM roles, or open a ticket to raise the quota.

-   Resource CREATE failed: ResponseException: resources.k8s\_NAT\_Gateway: The Account failed to create order. Code: OrderFailed

    **Solution:** Failed to create an order. Open a ticket for consultation.

-   Resource CREATE failed: ResponseException: resources.k8s\_master\_1: This operation is forbidden by Aliyun RiskControl system. Code: Forbidden.RiskControl

-   Resource CREATE failed: WaitConditionFailure: resources.k8s\_node\_cloudinit\_wait\_cond: See output value for more details.

    **Solution:** Failed to create a cluster. Try again later, or open a ticket for consultation.

-   Resource CREATE failed: WaitConditionTimeout: resources.k8s\_master1\_cloudinit\_wait\_cond: 0 of 2 received:

    **Solution:** Failed to create a cluster. Try again later, or open a ticket for consultation.

-   Resource CREATE failed: ResponseException: resources.k8s\_master\_1: The request processing has failed due to some unknown error. Code: UnknownError

    **Solution:** Unknown error. Try again later, or open a ticket for consultation.

-   Resource CREATE failed: ResponseException: resources.k8s\_nodes: The request processing has failed due to some unknown error. Code: UnknownError

    **Solution:** Unknown error. Try again later, or open a ticket for consultation.


