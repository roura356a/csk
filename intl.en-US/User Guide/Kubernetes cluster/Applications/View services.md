# View services {#task_qfv_ndn_vdb .task}

If the external service is configured when you create the application, in addition to running containers, Kubernetes dashboard creates the external services for pre-assigning the Server Load Balancer to bring traffic to the containers in the cluster.

1.   Log on to the [Container Service console](https://cs.console.aliyun.com). 
2.   Click Kubernetes \>**Application \>** \> **Service**in the left-side navigation pane. 
3.   Select the cluster and namespace from the Clusters and Namespace drop-down lists to view the deployed services. 

    You can view the name, type, created time, cluster IP address, internal endpoint, and external endpoint of a service. In this example, you can view the external endpoint \(IP address\) assigned to the service. Click the IP address to access the Nginx welcome page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6902/15395846374486_en-US.png)

    You can also enter the Kubernetes dashboard of the cluster and click **Services** in the left-side navigation pane to view the services.


