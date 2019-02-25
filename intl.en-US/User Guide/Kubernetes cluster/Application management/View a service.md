# View a service {#task_qfv_ndn_vdb .task}

If the external service is configured when you create the application, in addition to running containers, Kubernetes dashboard creates the external services for pre-assigning the Server Load Balancer to bring traffic to the containers in the cluster.

1.   Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  In the left-side navigation pane under Kubernetes, choose **Discovery and Load Balancing** \> **Service**. 
3.  Select the target cluster and namespace. Then click **Details** on the right of the target service. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16665/155109542811046_en-US.png) 

    You can view the service name, service type, service creation time, cluster IP address, external endpoint, and other information. In this example, the external endpoint \(IP address\) assigned to the service is displayed. To access the Nginx application, click this IP address.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16665/155109542811047_en-US.png)

    You can also open the Kubernetes dashboard of the target cluster and click **Services** in the left-side navigation pane to view all services of the cluster.


