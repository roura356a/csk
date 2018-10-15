# Failed to delete Kubernetes clusters: ROS stack cannot be deleted {#concept_rz1_lwb_wdb .concept}

## Root cause {#section_jkp_5wb_wdb .section}

Some resources are manually added \(for example, manually add a VSwitch under the Virtual Private Cloud \(VPC\) created by Resource Orchestration Service \(ROS\)\) under the resources created by ROS. ROS does not have permissions to delete those resources. This causes ROS to fail to process the VPC when deleting the Kubernetes resources and then the cluster fails to be deleted.

**Note:** For more information about the resources automatically created by ROS when the Kubernetes cluster is created, see [Create a Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

## Solutions {#section_zpk_wwb_wdb .section}

1.  If the cluster fails to be deleted \(the cluster status is **Failed to delete**\), go to the [ROS console](https://partners-intl.console.aliyun.com/#/ros).
2.  Select the region in which the cluster resides and find the stack `k8s-for-cs-{cluster-id}` corresponding to the cluster. You can see the status is **Failed to delete**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15845/15395743249799_en-US.png)

3.  Click the stack name to go to the stack details page. Click **Resource** in the left-side navigation pane.

    You can see what resources failed to be deleted. In this example, the VSwitch under Server Load Balancer failed to be deleted.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15845/15395743249800_en-US.png)

4.  Go to the console in which the resource that failed to be deleted resides and find that resource.

    In this example, log on to the VPC console and find the VPC in which the cluster resides. Find the VSwitch that failed to be deleted under that VPC.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15845/15395743249801_en-US.png)

5.  Click **Delete** at the right of the VSwtich to manually delete it.

    In this example, the VSwitch has resources to release and cannot be deleted.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15845/15395743249802_en-US.png)

    Manually release the resources under this VSwitch and try to delete this VSwitch again.

6.  Manually delete all the resources that failed to be deleted under the Kubernetes cluster in this way and try to delete the Kubernetes cluster again.

