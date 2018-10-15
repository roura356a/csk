# Delete a cluster {#task_kh11_5st_4y .task}

In the Container Service console, you can delete clusters that are no longer in use.

1.   Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.   Under Container Service Kubernetes, click **Clusters** in the left-side navigation pane. 
3.   Click **More** \> **Delete **and select the target cluster. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6887/15395831044338_en-US.png)


**Failed to delete a cluster**

If you manually add some resources under the resources created by Resource Orchestration Service \(ROS\), ROS does not have permissions to delete the manually added resources. For example, manually add a VSwitch under the Virtual Private Cloud \(VPC\) created by ROS. ROS fails to process this VPC when deleting the Kubernetes resources and then the cluster fails to be deleted.

Container Service allows you to force delete the cluster. You can force delete the cluster record and ROS stack if the cluster fails to be deleted. However, you must release the created resources manually.

The cluster status is Failed if the cluster fails to be deleted.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6887/15395831044339_en-US.png)

Click**More** \> **Delete **in the displayed dialog box, you can see the resource that failed to delete, check force delete, and click **OK**. The cluster and ROS resource can be deleted.

**Note:** You must manually release the resources that failed to be deleted. For information on how to troubleshoot the problem with resources that cannot be released, see [EN-US\_TP\_6954.md\#](reseller.en-US/User Guide/Kubernetes cluster/FAQ/Failed to delete Kubernetes clusters: ROS stack cannot be deleted.md#).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6887/15395831044340_en-US.png)

