# Use batch release on Alibaba Cloud Container Service for Kubernetes {#task_hyt_s1r_s2b .task}

You can use Alibaba Cloud Container Service for Kubernetes to release application versions in batches, achieving fast version verification and rapid iteration of applications.

**Note:** The latest Kubernetes cluster has installed alicloud-application-controller by default. For older versions of clusters, only versions of 1.9.3 and later are currently supported, and you can upgrade old versions of clusters through the prompt link on the console.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click **Application** \> **Release** in the left-side navigation pane. Click **Create batch release** in the upper-right corner. 

    **Note:** If the button is gray, you can upgrade the cluster by following the upgrade link.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15688055798975_en-US.png)

3.  Configure batch release information, including the application name, cluster, namespace, and options. Click **Next**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15688055798982_en-US.png)

4.  On the batch publishing configuration page, configure the backend pod and service, and then click **Update** to create an application. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15688055799004_en-US.png)

5.  Return to the release list, an application is displayed in the **Not started** status. Click **Detail** on the right. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15688055799005_en-US.png)

6.  On the application detail page, you can view more information. Click **Change Configuration** in the upper-right corner of the page to make a batch release change. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15688055799006_en-US.png)

7.  Configure changes for the new version of the application, and then click **Update**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15688055799011_en-US.png)

8.  By default, you return to the release list page, where you can view the batch release status of the application. After completing the first deployment, click **Detail**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15688055799014_en-US.png)

9.  You can see that the Not Started list is has two pods and the Completed list has two pods, which indicates that the first batch has been completed in batch release. Click **Continue**，you can release the second batch of pods. Click **Roll Back** to roll back to the previous version. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15688055799013_en-US.png)

10. When completing the release, click **History** to roll back to history versions. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15688055799015_en-US.png)


You can use batch release to quickly verify your application version without traffic consumption. Batch release is more resource-saving than blue-green release. Currently, batch release can be performed on only web pages. The yaml file editing is to be opened later to support more complex operations.

