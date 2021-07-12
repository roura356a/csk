# Delete an ACK cluster

This topic describes how to delete an ACK cluster in the Container Service for Kubernetes \(ACK\) console.

**Note:** After you delete an ACK cluster, the nodes that you manually added to the cluster are not automatically released.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to delete and choose **More** \> **Delete** in the Actions column.

4.  In the Delete Cluster dialog box, verify that the cluster is the one that you want to delete and select the resources that you want to retain. Then, select **I understand the above information and want to delete the specified cluster**, and click **OK**.

    The following figure shows an example of the Delete Cluster dialog box. The actual information displayed in the dialog box is based on the cluster that you want to delete.

    ![Screenshot](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0535359951/p51017.png)


## FAQ

Why do I fail to delete an ACK cluster?

Resource Orchestration Service \(ROS\) does not have permissions to delete resources that are manually added to an ROS stack. For example, ROS cannot release a virtual private cloud \(VPC\) that contains a manually added vSwitch. Consequently, the cluster deployed in the VPC cannot be deleted.

ACK allows you to forcibly delete clusters. If your first attempt to delete a cluster fails, you can forcibly delete the cluster and the ROS stack. However, when you forcibly delete a cluster, the resources that are created by ROS and the manually added resources are not released. You must manually release these resources.

When a cluster fails to be deleted, the following information is displayed, as shown in the figure.

![The cluster fails to be deleted](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0362331261/p10861.png)

1.  Find the cluster that fails to be deleted, choose **More** \> **Delete** in the Actions column.
2.  In the dialog box that appears, you can view the resources that failed to be deleted. Click **OK** to delete the cluster and ROS stack.

    ![Delete an ACK cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1535359951/p10862.png)

    **Note:** If you select **Retain Resources** in the Delete Cluster dialog box, the resources that you select are not released. You must manually release these resources. For more information about how to locate resources that cannot be released, see [Failed to delete Kubernetes clusters: ROS stack cannot be deleted]().


