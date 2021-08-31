# Delete an ACK cluster

This topic describes how to delete a Container Service for Kubernetes \(ACK\) cluster in the ACK console.

**Note:** After you delete an ACK cluster, the nodes that you manually added to the cluster are not automatically released.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to delete and choose **More** \> **Delete** in the Actions column.

4.  In the Delete Cluster dialog box, confirm that the cluster is the one that you want to delete and select the resources that you want to retain. Then, select **I understand the above information and want to delete the specified cluster**, and click **OK**.


## FAQ

**Issue 1: What do I do if an ACK cluster cannot be deleted?**

If you do not release the resources that are manually added to the resources created by Resource Orchestration Service \(ROS\), ROS cannot delete the resources it created when you delete the cluster. For example, ROS cannot release a virtual private cloud \(VPC\) that contains a manually added vSwitch. Consequently, the cluster deployed in the VPC cannot be deleted.

ACK allows you to forcibly delete clusters. If your first attempt to delete a cluster fails, you can forcibly delete the cluster and the ROS stack. However, when you forcibly delete a cluster, the resources that are created by ROS and the manually added resources are not released. You must manually release these resources.

When a cluster fails to be deleted, the following information is displayed, as shown in the figure.

![The cluster fails to be deleted](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0362331261/p10861.png)

1.  Find the cluster that fails to be deleted, choose **More** \> **Delete** in the Actions column.
2.  In the dialog box that appears, you can view the resources that failed to be deleted. Click **OK** to delete the cluster and ROS stack.

    ![Delete an ACK cluster](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1535359951/p10862.png)

    **Note:** If you select **Retain Resources** in the Delete Cluster dialog box, the resources that you select are not released. You must manually release these resources. For more information about how to locate the resources that cannot be released, see [Failed to delete Kubernetes clusters](~~86763~~).


**Issue 2: How do I disable the deletion protection of an ACK cluster?**

If deletion protection is enabled for an ACK cluster, the system prompts you to disable deletion protection when you delete the cluster. Perform the following steps to disable deletion protection:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  Find the cluster that you want to delete and choose **More** \> **Modify Deletion Protection Setting** in the **Actions** column.

4.  In the **Modify Deletion Protection Setting** dialog box, turn off **Cluster Deletion Protection**.


