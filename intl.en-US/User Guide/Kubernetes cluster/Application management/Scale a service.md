# Scale a service {#task_tgn_ycn_vdb .task}

This topic describes how to **scale** scale out or scale in an application service as needed after an application is created.

1.   Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  In the left-side navigation pane under Kubernetes, click **Clusters**. 
3.  On the right of the target cluster, click **Dashboard**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16664/155115908111052_en-US.png)

4.  In the left-side navigation pane, click **Deployments** to view created deployments. 
5.  Click the ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16664/155115908139603_en-US.png) icon on the right of the target deployment, and then click **Scale**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16664/155115908111053_en-US.png)

6.  In the displayed dialog box, change the value of **Desired number of pods** to 2 and then click **OK**. This action adds a new pod. The number of replicas becomes 2.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16664/155115908111054_en-US.png)


You can check the status of each Kubernetes object according to the icon on the left of the deployment list.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16664/155115908111055_en-US.png) indicates the object is being deployed. ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16664/155115908111056_en-US.png) indicates the object has been deployed.

Furthermore, you can click a deployment name to view the details of the running Web service. Specifically, you can view the replica sets included in the deployment, and the CPU usage and memory usage of these replica sets.

**Note:** You need to wait a few minutes if you see no resources.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16664/155115908111057_en-US.png)

