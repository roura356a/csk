# Implement a phased release in the ACK console

Container Service for Kubernetes \(ACK\) supports phased releases. You can use this feature to release different versions of an application and iterate applications in an efficient manner. This allows you to verify the functionality of a new application version.

**Note:** By default, alicloud-application-controller is installed for a cluster in version 1.9.3 or later. For a cluster of an earlier version, you can upgrade the cluster based on the instructions in the ACK console.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Releases**.

5.  Click the **Batch Release** tab. On the tab that appears, click **Create Batch Release** in the upper-right corner.

    **Note:** If the button is unavailable, you must upgrade the cluster based on the instructions in the ACK console.

6.  In the Batch Release Basic Information step, specify parameters **Name**, **Cluster**, **Namespace**, and **Release Option**. Click **Next**.

    ![Configure parameters for a phased release](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3455359951/p8982.png)

7.  In the Batch Release Configuration step, configure pods and services and click **Update**. An application is created.

    ![Create an application](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3455359951/p9004.png)

8.  Return to the Batch Release tab. You can find the newly created release and its status is **Not Started**. To view the details of the release, click **View Details** in the Actions column.

    ![View release details 1](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3455359951/p9005.png)

9.  On the details page of the release, view release details. To update the release, click **Change Configuration** in the upper-right corner.

10. On the page that appears, modify the parameter values for the release and click **Update**.

    ![Update the release](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4455359951/p9011.png)

11. By default, the Batch Release tab appears. You can view the status of the release. After the first version is deployed, click **View Details** in the Actions column.

    ![View release details 2](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4455359951/p9014.png)

12. On the details page of the release, view release details. The Not Started tab displays two pods and the Completed tab displays two pods. This indicates that the first version is deployed.**** To deploy the second version, click **Continue**. To roll back to the previous version, click **Roll Back**.

    ![Rollback](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4455359951/p9013.png)

13. After the release is completed, click the **History** tab and select a history version to which you want to roll back.

    ![Roll back to a history version](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4455359951/p9015.png)


The phased release feature allows you to verify the functionality of a new application version without splitting traffic from the old version. Therefore, a phased release consumes fewer resources than a blue-green release. You can implement a phased release in the ACK console. Support for YAML files will be available soon.

