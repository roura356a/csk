# Network configurations for creating an application from an image

This topic describes the differences in network configurations when you create an application from an image in a cluster of Container Service for Swarm and a cluster of Container Service for Kubernetes \(ACK\).

## Create an application from an image

The interfaces for creating an application from an image in a Swarm cluster and in an ACK are quite different.

-   For more information about how to create an application from an image in a Swarm cluster, see [Create an application](/intl.en-US/User Guide/Applications/Create an application.md).
-   For more information about how to create an application from an image in an ACK cluster, see [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

Network configurations

In Swarm clusters, network configurations are used to expose applications to external access.

## Port mapping

Swarm cluster

You can configure **port mapping** to map the application port to a port on the host. If you set the same port on all hosts, you can access the application by requesting `<HostIP>:<Port>`.

![Swarm cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35343.png)

ACK cluster

You can create a **NodePort** type Service to expose your application to external access. You can use one of the following methods to create a Service.

## Method 1: Create a NodePort type Service when you create an application

1.  After you complete the settings on the Container wizard page, proceed to the Advanced wizard page. In the Access Control section, click **Create** on the right side of **Services**.

    ![Advanced settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5546858951/p35379.png)

2.  Select **Node Port** from the **Type** drop-down list. For more information, see [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

    ![Node Port](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35381.png)


## Method 2: Directly create a NodePort type Service

1.  In the left-side navigation pane of the ACK console, choose **Ingresses and Load Balancing** \> **Services**. The Services page appears.

2.  Select a cluster and namespace to deploy the Service. In the upper-right corner of the Services page, click **Create**. In the Create Service dialog box, select **Node Port** from the **Type** drop-down list. For more information, see [t16662.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Create a service.md).

    ![Create a Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35387.png)


## Simple routing

Swarm cluster

You can configure **simple routing** to expose your application through a domain name. You can specify a custom domain name or use the default domain that is provided by ACK.

![Simple routing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35393.png)

ACK cluster

You can create an Ingress to implement simple routing and other related features. You can also use Ingresses to implement blue-green deployment and canary release. For more information, see [Overview](/intl.en-US/Best Practices/Release/Implement a canary release and a blue/green deployment through Ingress in a Kubernetes cluster/Overview.md).

## Method 1: Create an Ingress when you create an application

You can use one of the following methods to create an Ingress.

1.  After you complete the settings on the Container wizard page, proceed to the Advanced wizard page. In the Access Control section, click **Create** on the right side of **Ingresses**.

    ![Advanced settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35395.png)

2.  For more information, see [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

    ![Ingress settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35397.png)


## Method 2: Directly create an Ingress

1.  In the left-side navigation pane of the ACK console, choose **Ingresses and Load Balancing** \> **Ingresses**. The Ingresses page appears.

2.  Select a cluster and namespace to deploy the Ingress. In the upper-right corner of the Ingresses page, click **Create**. For more information, see [Create an Ingress on the web UI](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Create an Ingress on the web UI.md).

    ![Ingresses and Load Balancing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35397.png)


## Load balancing

Swarm cluster

You can configure **SLB routing** to use Server Load Balancer \(SLB\) to expose your application. You must create an SLB instance and bind the instance IP and port with your application. Then, you can access the application by requesting <SLB\_IP\>:<Port\>.

![SLB routing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4546858951/p35439.png)

ACK cluster

You can also use SLB to expose your application. You do not need to create an SLB instance. Instead, you can create a LoadBalancer type Service, which automatically creates an SLB instance for you. You can specify whether the SLB instance is used for public access or internal access. If you use a YAML file to create a LoadBalancer type Service, you can use an existing SLB instance and configure session persistence. For more information, see [t16662.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Create a service.md).

## Method 1: Create a LoadBalancer type Service when you create an application

You can use one of the following methods to create a LoadBalancer type Service:

1.  After you complete the settings on the Container wizard page, proceed to the Advanced wizard page. In the Access Control section, click **Create** on the right side of **Services**.

    ![Advanced settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5546858951/p35379.png)

2.  Select **Server Load Balancer** from the **Type** drop-down list. For more information, see [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

    ![SLB](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5546858951/p35440.png)


## Method 2: Directly create a LoadBalancer type Service

1.  In the left-side navigation pane of the ACK console, choose **Ingresses and Load Balancing** \> **Services**. The Services page appears.

2.  Select a cluster and namespace to deploy the Service. In the upper-right corner of the Services page, click **Create**. In the Create Service dialog box, select **Server Load Balancer** from the **Type** drop-down-list. For more information, see [t16662.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Create a service.md).

    ![SLB](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5546858951/p35441.png)


