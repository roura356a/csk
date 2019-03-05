# Scale a service {#task_tgn_ycn_vdb .task}

This topic describes how to scale out or scale in an application service as needed after an application is created.

1.   Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  In the left-side navigation pane under Kubernetes, choose **Clusters** \> **Clusters**. 
3.  On the right of the target cluster, click **Dashboard**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16664/155176516611052_en-US.png)

4.  In the left-side navigation pane, click **Deployments**. 
5.  Click the ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16664/155176516639603_en-US.png) icon on the right of the target deployment, and then click **Scale**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16664/155176516611053_en-US.png)

6.  In the displayed dialog box, change the value of **Desired number of pods** to the number you require. Here, the example number of desired pods is 2. Then, click **OK**. This action adds a new pod. The number of replicas becomes 2.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16664/155176516611054_en-US.png)


You can check the status of each Kubernetes object according to the icon on the left of the deployment list.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16664/155176516611055_en-US.png) indicates the object is being deployed. ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16664/155176516611056_en-US.png) indicates the object has been deployed.

Additionally, you can click a deployment name to view the details of the running Web service. Specifically, you can view the replica sets included in the deployment, and the CPU usage and memory usage of these replica sets.

**Note:** If no resources are displayed, we recommend that you wait a few minutes and then refresh the page.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16664/155176516611057_en-US.png)

