# Delete a Kubernetes cluster {#task_kh11_5st_4y .task}

This topic describes how to delete a Kubernetes cluster in the Alibaba Cloud Container Service for Kubernetes console.

1.   Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Clusters**. 
3.  On the right of the target cluster, choose **More** \> **Delete **. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16650/155228638510860_en-US.png)


**Troubleshoot a cluster deletion failure**

If you manually add cloud resources into resources created by Resource Orchestration Service \(ROS\), ROS does not have the permission to delete the manually added resources. For example, if you manually add a VSwitch in the Virtual Private Cloud \(VPC\) created by ROS, ROS cannot delete this VPC when you delete the Kubernetes cluster. As a result, the cluster deletion will fail.

To solve this issue, Container Service allows you to forcibly delete the cluster. Specifically, you can delete the cluster record and the ROS stack if the cluster fails to be deleted. However, you must release the created resources manually.

If a cluster fails to be deleted, the cluster status is displayed as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16650/155228638510861_en-US.png)

On the right of the cluster that failed to be deleted, choose **More** \> **Delete **. In the displayed dialog box, select the **Force Delete** check box, and then click **OK**.

**Note:** You must manually release the resources that failed to be deleted. For information, see [Failed to delete Kubernetes clusters: ROS stack cannot be deleted](../../../../../reseller.en-US/FAQ/Failed to delete Kubernetes clusters: ROS stack cannot be deleted.md#).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16650/155228638510862_en-US.png)

