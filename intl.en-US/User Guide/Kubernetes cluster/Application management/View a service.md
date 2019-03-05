# View a service {#task_qfv_ndn_vdb .task}

This topic describes how to view a Kubernetes service in Alibaba Cloud Container Service for Kubernetes.

If you set an external service when you create an application, the Kubernetes dashboard creates the external service, in addition to running containers. The service is used to pre-set a Server Load Balancer to distribute traffic to the containers.

1.   Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  In the left-side navigation pane under Kubernetes, choose **Discovery and Load Balancing** \> **Service**. 
3.  Select the target cluster and namespace, and then click **Details** on the right of the target service. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16665/155176533511046_en-US.png) 

    You can view the service name, service type, service creation time, cluster IP address, external endpoint, and other information. In this example, the external endpoint \(IP address\) assigned to the service is displayed. To access the Nginx application, click this IP address.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16665/155176533511047_en-US.png)

    You can also open the Kubernetes dashboard of the target cluster and click **Services** in the left-side navigation pane to view all services of the cluster.


