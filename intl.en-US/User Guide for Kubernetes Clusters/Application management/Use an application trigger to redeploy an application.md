# Use an application trigger to redeploy an application {#task_hgt_52w_w2b .task}

Alibaba Cloud Container Service Kubernetes supports the application trigger function. You can use an application trigger in many ways.

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US//Create a Kubernetes cluster.md#).
-   You have created an application that is used to create an application trigger and test the trigger. In this example, create an nginx application.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click **Application** \> **Deployment** and select a cluster and namespace. Click **Details** at the right of the target nginx application. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17802/15646487359892_en-US.png)

3.  On the nginx application details page, click **Create Trigger** on the right side of the trigger bar. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17802/15646487359898_en-US.png)

4.  In the pop-up dialog box, click **Redeploy** and click **Confirm**. 

    **Note:** Currently, only the redeploy action is supported.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17802/15646487359899_en-US.png)

    After the trigger is created, a trigger link is displayed in the trigger bar on the nginx application detail page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17802/15646487359900_en-US.png)

5.  Copy the trigger link and visit it in the browser. A message is returned on the web page, containing information such as the request ID. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17802/15646487359901_en-US.png)

6.  Back to the nginx application detail page, you can see that a new pod appears. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17802/15646487359904_en-US.png)

    After a period of time, the nginx application removes the old pod and keeps only the new pod.


You can call a trigger by using GET or POST in a third-party system. For example, you can run the curl command to call a trigger.

Call the redeploy trigger as follows:

 `curl https://cs.console.aliyun.com/hook/trigger?token=xxxxxxx`

