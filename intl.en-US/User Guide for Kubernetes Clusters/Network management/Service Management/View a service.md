# View a service

If you create an application that provides external services,the Kubernetes dashboard automatically creates a service of the LoadBalancer type. This service uses a Server Load Balancer \(SLB\) instance. This allows you to direct external requests for access to pods inside the cluster. This topic describes how to view a Kubernetes service in the Container Service for Kubernetes \(ACK\) console.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Services**.

5.  Specify a cluster and a namespace, find the service that you want to view, and then click **Details** in the Actions column of the service.

    You can view the information such as the name, type, creation time, cluster IP address, and external endpoint of the service. The following figure shows the external endpoint \(IP address\) that is assigned to the service. To access the NGINX application, click this IP address.

    ![Endpoint](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7345359951/p11047.png)

6.  You can also go to the Kubernetes dashboard. In the left-side navigation pane, click **Services** to view all services.


