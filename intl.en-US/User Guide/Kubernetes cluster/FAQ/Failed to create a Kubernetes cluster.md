# Failed to create a Kubernetes cluster {#concept_l1t_3vb_wdb .concept}

## Check the cause of failure {#section_mct_jvb_wdb .section}

You can check the cause of cluster creation failure by viewing the cluster creation events.

Log on to the [Resource Orchestration Service \(ROS\) console](https://ros.console.aliyun.com/).

Select the region in which the cluster resides. Click **Manage** at the right of the cluster. Click **Event**in the left-side navigation pane. Move the cursor over the failed event to view the specific error message of the failure.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6953/15347574504730_en-US.png)

If the preceding error message is displayed, it means that the cluster creation failed because the number of Virtual Private Cloud \(VPC\) instances has reached the quota.

## Failure codes and solutions {#section_dtt_1wb_wdb .section}

-   **Code: QuotaExceeded.Eip, Message: Elastic IP address quota exceeded**

    **Solution:** Release unused EIPs, or open a ticket to raise the EIP quota.

-   **The maximum number of SLB instances is exceeded. Code: ORDER.QUANTITY\_INVALID**

    **Solution:** Release unused SLB instances, or open a ticket to raise the SLB quota.

-   **Resource CREATE failed: ResponseException: resources.k8s\_vpc: VPC quota exceeded. Code: QuotaExceeded.Vpc**

    **Solution:** Release unused VPCs, or open a ticket to raise the VPC quota.

-   **Resource CREATE failed: ResponseException: resources.k8s\_master\_1: The specified image does not support cloud-init. Code: ImageNotSupportCloudInit**

    **Solution:** When using custom image to create a cluster, the custom image used must be developed based on the lastest Centos public cloud image.

-   **Status Code: 403 Code: InvalidResourceType.NotSupported Message: This resource type is not supported;**

    **Solution:** ECS is out of stock or the type of ECS instances you selected are not supported.


