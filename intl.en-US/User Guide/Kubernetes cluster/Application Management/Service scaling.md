# Service scaling {#task_tgn_ycn_vdb .task}

After an application is created, you can scale out or in the services as per your needs.

1.   Log on to the [Container Service console](https://cs.console.aliyun.com). 
2.   Click Kubernetes \> **Clusters** in the left-side navigation pane. 
3.   Click **Dashboard** at the right of the cluster to enter the Kubernetes dashboard. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6901/15330247754480_en-US.png)

4.   In the Kubernetes dashboard, click **Deployments** in the left-side navigation pane to view the created deployments. 
5.   Click the icon at the right of the deployment and then select **Scale**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6901/15330247754481_en-US.png)

6.   The Scale a Deployment dialog box appears. Modify the value of **Desired number of pods** to 2 and then click **OK**. Then, a pod is added by expansion and the number of replicas rises to 2.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6901/15330247754482_en-US.png)


You can check the status of each Kubernetes object according to the icon on the left.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6901/15330247754483_en-US.png) indicates the object is being deployed.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6901/15330247754484_en-US.png) indicates the object has completed the deployment.

After the application completes the deployment, you can click a deployment name to view the details of the running Web service. You can view the replica sets in the deployment, and the CPU usage and memory usage of these replica sets. You can also click Pods in the left-side navigation pane, open a pod, and click **LOGS** in the upper-right corner to view the container logs.

**Note:** Wait a few minutes if you cannot view any resources.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6901/15330247754485_en-US.png)

