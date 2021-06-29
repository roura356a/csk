---
keyword: [use an internal-facing SLB instance to access a Kubernetes service, Ingress controller]
---

# Configure an Ingress controller to use an internal-facing SLB instance

You can configure Container Service for Kubernetes \(ACK\) clusters to allow access from the Internet and access from other services in the same virtual private cloud \(VPC\). This topic describes how to configure an Ingress controller to use an internal-facing Server Load Balancer \(SLB\) instance.

-   A Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   You are connected to a master node of the cluster by using SSH. For more information, see [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use SSH to connect to an ACK cluster.md).

When you create a Kubernetes cluster by using the Container Service console, the system automatically deploys an NGINX Ingress controller in the cluster and associates it with an Internet-facing SLB instance during cluster initialization.

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1836858951/p81310.png)

## Configure an internal-facing SLB instance

You can modify the configuration of the NGINX Ingress controller to make the cluster accessible to only services that are deployed in the same VPC.

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2836858951/p81311.png)

1.  Create an internal-facing SLB instance. For more information, see [Create a CLB instance](/intl.en-US/Classic Load Balancer/User Guide/Instance/Create a CLB instance.md).

    **Note:** Create an SLB instance in the same VPC as the cluster. Choose the instance type based on your requirements.

2.  Configure the NGINX Ingress controller.

    After you have created an internal-facing SLB instance, configure the NGINX Ingress controller to use the SLB instance with the following annotations. For more information, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).

    ```
    # nginx ingress slb service
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-ingress-lb
      namespace: kube-system
      labels:
        app: nginx-ingress-lb
      annotations:
        # Specify that the SLB instance uses an internal IP address 
        service.beta.kubernetes.io/alicloud-loadbalancer-address-type: intranet
        # Specify the ID of the created internal-facing SLB instance. 
        service.beta.kubernetes.io/alicloud-loadbalancer-id: <YOUR_INTRANET_SLB_ID>
        # Specify whether to automatically create listeners, which overwrite existing listeners. You can also manually create listeners. 
        #service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: 'true'
    spec:
      type: LoadBalancer
      # route traffic to other nodes
      externalTrafficPolicy: "Cluster"
      ports:
      - port: 80
        name: http
        targetPort: 80
      - port: 443
        name: https
        targetPort: 443
      selector:
        # select app=ingress-nginx pods
        app: ingress-nginx
    ```

    After the configuration is applied, the NGINX Ingress controller \(kube-system/nginx-ingress-lb\) uses the newly specified internal-facing SLB instance.


## Use an internal-facing SLB instance and an Internet-facing SLB instance together

In some scenarios, you may want the cluster to allow access from the Internet and access from other services in the same VPC at the same time. To do this, you need only to deploy another NGINX Ingress controller \(for example, kube-system/nginx-ingress-lb-intranet\) in the cluster.

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2836858951/p81334.png)

**Note:** By default, a kube-system/nginx-ingress-lb Ingress controller is created during cluster initialization. This Ingress controller uses an Internet-facing SLB instance.

1.  Create an internal-facing SLB instance. For more information, see [Create a CLB instance](/intl.en-US/Classic Load Balancer/User Guide/Instance/Create a CLB instance.md).

    **Note:** Create an SLB instance in the same VPC as the cluster. Choose the instance type based on your requirements.

2.  Create a new NGINX Ingress controller.

    After you have created an internal-facing SLB instance, you can use the following YAML file to create a kube-system/nginx-ingress-lb-intranet service.

    ```
    # intranet nginx ingress slb service
    apiVersion: v1
    kind: Service
    metadata:
      # Set the service name to nginx-ingress-lb-intranet. 
      name: nginx-ingress-lb-intranet
      namespace: kube-system
      labels:
        app: nginx-ingress-lb-intranet
      annotations:
        # Specify that the SLB instance uses an internal IP address 
        service.beta.kubernetes.io/alicloud-loadbalancer-address-type: intranet
        # Specify the ID of the created internal-facing SLB instance. 
        service.beta.kubernetes.io/alicloud-loadbalancer-id: <YOUR_INTRANET_SLB_ID>
        # Specify whether to automatically create listeners, which overwrite existing listeners. You can also manually create listeners. 
        #service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: 'true'
    spec:
      type: LoadBalancer
      # route traffic to other nodes
      externalTrafficPolicy: "Cluster"
      ports:
      - port: 80
        name: http
        targetPort: 80
      - port: 443
        name: https
        targetPort: 443
      selector:
        # select app=ingress-nginx pods
        app: ingress-nginx
    ```


After the kube-system/nginx-ingress-lb-intranet service is created, run the `kubectl -n kube-system get svc | grep nginx-ingress-lb` command and verify that two NGINX Ingress controllers are running. One is associated with an Internet-facing SLB instance, and the other with an internal-facing SLB instance.

```
 kubectl -n kube-system get svc | grep nginx-ingress-lb
```

```
nginx-ingress-lb            LoadBalancer   172.1*.*.**    47.96.2**.**   80:31456/TCP,443:30016/TCP   5h
nginx-ingress-lb-intranet   LoadBalancer   172.19.*.***   192.16*.*.**   80:32394/TCP,443:31000/TCP   7m
```

When you expose services through Ingresses, you can allow Internet access through the Internet-facing SLB instance and also access from other services in the same VPC network through the internal-facing SLB instance.

