# Network configurations for deploying an application from an image

This topic describes the differences in network configurations when you deploy an application from an image in a Container Service for Swarm cluster and in a Container Service for Kubernetes \(ACK\) cluster.

## Deploy an application from an image

The user interfaces for deploying an application from an image in a Container Service for Swarm cluster and in an ACK cluster are quite different.

-   For more information about how to deploy an application from an image in a Container Service for Swarm cluster, see [Create an application](/intl.en-US/User Guide/Applications/Create an application.md).
-   For more information about how to deploy an application from an image in an ACK cluster, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

Network configurations

In Container Service for Swarm clusters, network configurations are used to expose applications to external access.

## Port mapping

**Container Service for Swarm cluster**

You can configure **port mapping** to map the application port to a port on the host. After you specify a host port number, the application port is mapped to this port on each host. You can access the application by sending requests to `<HostIP>:<Port>`.

![Container Service for Swarm cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35343.png)

**ACK cluster**

You can create a **NodePort** Service to expose your application to external access. You can use one of the following methods to create a Service:

## Method 1: Create a NodePort Service when you deploy an application

1.  After you complete the settings on the Container wizard page, proceed to the Advanced wizard page. In the Access Control section, click **Create** on the right side of **Services**.

    ![Advanced](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5546858951/p35379.png)

2.  Select **Node Port** from the **Type** drop-down list. For more information, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

    ![Node Port](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35381.png)


## Method 2: Directly create a NodePort Service

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Services**.

5.  Select the namespace to which the Service belongs. In the upper-right corner of the Services page, click **Create**. In the Create Service dialog box, select **Node Port** from the **Type** drop-down list. For more information, see [Manage Services](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Manage Services.md).

    ![Create a Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35387.png)


## Simple routing

Container Service for Swarm cluster

You can configure **simple routing** to expose your application through a domain name. You can specify a custom domain name or use the default domain name that is provided by Container Service for Swarm.

![Simple routing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35393.png)

ACK cluster

You can create an Ingress to implement simple routing and other related features. You can also use Ingresses to implement blue-green releases and canary releases for applications in ACK clusters. For more information, see [Overview]().

## Method 1: Create an Ingress when you deploy an application

You can use one of the following methods to create an Ingress:

1.  After you complete the settings on the Container wizard page, proceed to the Advanced wizard page. In the Access Control section, click **Create** on the right side of **Ingresses**.

    ![Advanced](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35395.png)

2.  Deploy a stateless application from an image. For more information, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

    ![Container configurations](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35397.png)


## Method 2: Directly create an Ingress

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Ingresses**.

5.  Select the namespace to which the Ingress belongs and click **Create** in the upper-right corner of the Ingresses page. For more information, see [t16682.md\#section\_g4f\_wex\_ruo](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md).

    ![Ingresses and Load Balancing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35397.png)


## Load balancing

Container Service for Swarm cluster

You can configure **load balancing** to expose your application by using Server Load Balancer \(SLB\). You must create an SLB instance and associate the instance IP and port with your application. Then, you can access the application by sending requests to <SLB\_IP\>:<Port\>.

![Load balancing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35439.png)

ACK cluster

You can also use an SLB instance to expose your application that is deployed in an ACK cluster. You do not need to manually create and configure an SLB instance. A LoadBalancer Service automatically creates an SLB instance for you. You can specify whether the SLB instance is used to enable access over the Internet or a private network. If you create a LoadBalancer Service by using a YAML template, you can specify to use an existing SLB instance and enable the session persistence feature. For more information, see [Manage Services](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Manage Services.md).

## Method 1: Create a LoadBalancer Service when you deploy an application

You can use one of the following methods to create a LoadBalancer Service:

1.  After you complete the settings on the Container wizard page, proceed to the Advanced wizard page. In the Access Control section, click **Create** on the right side of **Services**.

    ![Advanced](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5546858951/p35379.png)

2.  Select **Server Load Balancer** from the **Type** drop-down list. For more information, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

    ![Server Load Balancer](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5546858951/p35440.png)


## Method 2: Directly create a LoadBalancer Service

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Services**.

5.  Select the namespace to which the Service belongs. In the upper-right corner of the Services page, click **Create**. In the Create Service dialog box, select **Server Load Balancer** from the **Type** drop-down-list. For more information, see [Manage Services](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Manage Services.md).

    ![Server Load Balancer](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5546858951/p35441.png)


