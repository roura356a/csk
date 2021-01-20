---
keyword: [Access Kubernetes workloads through internal SLB instances, Ingress controller]
---

# Configure an ingress controller to use an internal SLB instance

Container Service clusters allow Internet access and also access from other workloads in the same VPC network. This topic describes how to configure an ingress controller to use Server Load Balancer \(SLB\).

-   You have created a Kubernetes cluster. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
-   You can use SSH to connect to a master node of the cluster. For more information, see [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use SSH to connect to an ACK cluster.md).

When you create a Kubernetes cluster through the Container Service console, the system automatically deploys an NGINX ingress controller in the cluster and associates it with a public SLB instance during cluster initialization.

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1836858951/p81310.png)

## Configure an internal SLB instance

To allow access from other workloads in the same VPC network only, you need to modify the configurations of the NGINX ingress controller.

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2836858951/p81311.png)

1.  Create an internal SLB instance. For more information, see [Create an SLB instance](/intl.en-US/Classic Load Balancer/User Guide/Instance/Create an SLB instance.md).

    **Note:** Create an SLB instance in the same VPC network as the target cluster. Choose the instance type based on your needs.

2.  Configure the NGINX ingress controller.

    After you have created an internal SLB instance, configure the NGINX ingress controller to use the SLB instance with the following annotations. For more information, see [Use annotations to configure SLB instances](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md).

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
        # Set this parameter to the ID of your internal SLB instance
        service.beta.kubernetes.io/alicloud-loadbalancer-id: <YOUR_INTRANET_SLB_ID>
        # Specify whether to automatically create listeners, which will overwrite existing listeners. You can also manually create listeners.
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

    After the configuration is saved, the NGINX ingress controller \(kube-system/nginx-ingress-lb\) now uses the newly specified internal SLB instance.


## Use an internal SLB instance and a public SLB instance together

In some scenarios, you may want to allow Internet access and access from other workloads in the same VPC network at the same time. This needs you to deploy another NGINX ingress controller \(for example, kube-system/nginx-ingress-lb-intranet\) in the cluster.

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2836858951/p81334.png)

**Note:** By default, a kube-system/nginx-ingress-lb ingress controller is created during cluster initialization. This ingress controller uses a public SLB instance.

1.  Create an internal SLB instance. For more information, see [Create an SLB instance](/intl.en-US/Classic Load Balancer/User Guide/Instance/Create an SLB instance.md).

    **Note:** Create an SLB instance in the same VPC network as the target cluster. Choose the instance type based on your needs.

2.  Create a new NGINX ingress controller.

    After you have created an internal SLB instance, you can use the following YAML file to create a kube-system/nginx-ingress-lb-intranet service.

    ```
    # intranet nginx ingress slb service
    apiVersion: v1
    kind: Service
    metadata:
      # Set the service name to nginx-ingress-lb-intranet
      name: nginx-ingress-lb-intranet
      namespace: kube-system
      labels:
        app: nginx-ingress-lb-intranet
      annotations:
        # Specify that the SLB instance uses an internal IP address
        service.beta.kubernetes.io/alicloud-loadbalancer-address-type: intranet
        # Set this parameter to the ID of your internal SLB instance
        service.beta.kubernetes.io/alicloud-loadbalancer-id: <YOUR_INTRANET_SLB_ID>
        # Specify whether to automatically create listeners, which will overwrite existing listeners. You can also manually create listeners.
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


After the kube-system/nginx-ingress-lb-intranet service is created, run the `kubectl -n kube-system get svc | grep nginx-ingress-lb` command and verify that two NGINX ingress controllers are running. One is associated with a public SLB instance, and the other with an internal SLB instance.

```
 kubectl -n kube-system get svc | grep nginx-ingress-lb
nginx-ingress-lb            LoadBalancer   172.19.9.26    47.96.223.50   80:31456/TCP,443:30016/TCP   5h
nginx-ingress-lb-intranet   LoadBalancer   172.19.4.140   192.168.2.88   80:32394/TCP,443:31000/TCP   7m
```

When you expose the service through ingresses, you can allow Internet access through the public SLB instance and also access from other workloads in the same VPC network through the internal SLB instance.

