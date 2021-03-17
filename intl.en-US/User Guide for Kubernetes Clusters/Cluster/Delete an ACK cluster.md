# Delete an ACK cluster

You can delete clusters of Container Service for Kubernetes \(ACK\) in the ACK console.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to delete and choose **More** \> **Delete** in the Actions column.

4.  In the Delete Cluster dialog box, verify that the cluster is the one you want to delete, select the cloud resources that you want to retain, select **I understand the above information and want to delete the specified cluster**, and then click **OK**.

    The following figure shows an example of the Delete Cluster dialog box. The actual information displayed in the dialog box depends on the cluster that you want to delete.

    ![Screenshot](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0535359951/p51017.png)


## FAQ

Why do I fail to delete an ACK cluster?

Resource Orchestration Service \(ROS\) does not have permissions to delete resources that are manually added to an ROS stack. For example, ROS cannot release a virtual private cloud \(VPC\) that contains a manually added VSwitch. Consequently, the cluster deployed in the VPC cannot be deleted.

ACK allows you to forcibly delete clusters. If your first attempt to delete a cluster fails, you can forcibly delete the cluster and the ROS stack. However, when you forcibly delete a cluster, the ROS-created resources and the manually added resources are not released. You must manually release these resources.

An error message appears when a cluster fails to be deleted, as shown in the following figure.

![The cluster fails to be deleted.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1535359951/p10861.png)

1.  After your first attempt to delete a cluster fails, choose **More** \> **Delete** in the Actions column.
2.  In the dialog box that appears, you can view the resources that failed to be deleted. Click **OK** to delete the cluster and ROS stack.

    ![Delete an ACK cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1535359951/p10862.png)

    **Note:** In the dialog box that appears, if you select **Retain Resources**, the ROS-created resources and the manually added resources are both retained. You must manually release these resources.


