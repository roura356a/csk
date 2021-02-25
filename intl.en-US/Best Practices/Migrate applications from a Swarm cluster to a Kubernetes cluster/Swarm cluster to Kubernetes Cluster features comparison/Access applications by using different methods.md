# Access applications by using different methods

This topic compares the methods that are used to access applications in a Container Service for Swarm cluster and in a Container Service for Kubernetes \(ACK\) cluster. The methods allow an application to access other applications within the same cluster or in other clusters.

## Access applications within the same cluster

Container Service for Swarm cluster

To access a Service within the same Container Service for Swarm cluster, you can use the `links` label to specify the name of the Service as an environment variable.

For example, as described in [YAML file configurations](/intl.en-US/Best Practices/Comparison between Swarm and Kubernetes cluster functions/YAML files used for creating applications.md), web services provided by a WordPress application are associated with a MySQL Service. You can access the MySQL Service by specifying its name mysql after the container is started.

```
links:            #---7
    - 'db:mysql'
```

ACK cluster

To access a Service within the same ACK cluster, you can use the cluster IP or name of the Service. We recommend that you use Service names to access applications within the same cluster.

When you create an application, you can specify the name of the Service that you want to access as an environment variable.

For example, as described in [YAML file configurations](/intl.en-US/Best Practices/Comparison between Swarm and Kubernetes cluster functions/YAML files used for creating applications.md), a WordPress application uses an environment variable to call a MySQL Service.

```
spec:    
      containers:    
      - image: wordpress:4   
        name: wordpress
        env:    
        - name: WORDPRESS_DB_HOST
          value: wordpress-mysql    #---7 Specify the name of the MySQL Service that you want to access. The name is mysql in this example.
        - name: WORDPRESS_DB_PASSWORD    
```

## Access applications in other clusters

Access an application by using a domain name

**Note:**

-   Ensure the network connectivity to the classic network or virtual private cloud \(VPC\).
-   The DNS server supports load balancing, which can distribute traffic to different backend IP addresses.
-   Applications that are accessed through domain names can be migrated from Swarm to Kubernetes without service disruption.

Simple routing \(use a domain name associated with the default SLB instance of a Container Service for Swarm cluster\)

To migrate an application from a Container Service for Swarm cluster to an ACK cluster, you must first create an application in the ACK cluster. You must verify that the application functions as expected before you migrate the Swarm application to the ACK cluster.

![Simple routing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1646858951/p36481.png)

Migration scheme

-   Perform the following operations to create an application in the ACK cluster:
    1.  In the ACK cluster, create an application of the same type as the application that you want to migrate from a Container Service for Swarm cluster.
    2.  Create a Loadbalancer Service for the application in the ACK cluster.
    3.  The Loadbalancer Service automatically creates a Server Load Balancer \(SLB\) instance. The IP address of the SLB instance is 2.2.2.2 in this example.
    4.  Modify the DNS record to map the IP address 2.2.2.2 to the domain name test.com.
-   Verify that the application in the ACK cluster is available

    The application is available if it can be accessed through IP address 2.2.2.2.

-   Switch traffic to the Kubernetes application

    Modify the DNS record to remove the backend IP address 1.1.1.1 that is mapped to the domain name test.com.


All traffic destined for the application in the Container Service for Swarm cluster is forwarded to the Kubernetes application through DNS.

Simple routing \(use a domain name associated with a self-managed SLB instance of a Container Service for Swarm cluster\)

You can associate a domain name with the default SLB instance or a self-managed SLB instance that is attached to a Container Service for Swarm cluster. The two methods differ in the following ways:

-   The SLB instances are different.
-   By default, Alibaba Cloud DNS is used. To use a custom domain name, you must manually resolve the domain name.

Migration scheme

The procedure is the same as the procedure for migrating an application from Swarm to Kubernetes by using the default SLB instance. You must create an application in the ACK cluster and verify that the application functions as expected before you switch all traffic to the Kubernetes application.

![Migration scheme](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1646858951/p36492.png)

Access through <HostIP\>:<port\>

If an application is accessed through <HostIP\>:<port\>, you cannot migrate the application from a Container Service for Swarm cluster to an ACK cluster without service disruption. We recommend that you migrate the application during off-peak hours.

Migration scheme

1.  Create an application in the ACK cluster and use a NodePort Service to expose the application. For more information, see [t83224.md\#section\_fbl\_gbt\_ggb](/intl.en-US/Best Practices/Comparison between Swarm and Kubernetes cluster functions/Network settings used for creating an application through an image.md).
2.  Record the NodePort and replace the <port\> of the Container Service for Swarm cluster with the <NodePort\> of the ACK cluster.

    **Note:** To perform this step, you must stop and modify applications one after another.

3.  Attach worker nodes of the ACK cluster to the SLB instance that is attached to the Container Service for Swarm cluster.
4.  The SLB instance forwards a percentage of traffic to the Kubernetes application. After you verify that the application functions as expected, you can remove the nodes of the Container Service for Swarm cluster from the SLB instance. Then, all production traffic is routed to the Kubernetes application.

Access through an SLB instance

If an application is accessed through an SLB instance, you cannot migrate the application from a Container Service for Swarm cluster to an ACK cluster without service disruption. We recommend that you migrate the application during off-peak hours.

Migration scheme

You can use LoadBalancer Services in the ACK cluster in the same way as you use SLB instances in the Container Service for Swarm cluster. For more information, see [t83224.md\#section\_wwh\_nbt\_ggb](/intl.en-US/Best Practices/Comparison between Swarm and Kubernetes cluster functions/Network settings used for creating an application through an image.md).

