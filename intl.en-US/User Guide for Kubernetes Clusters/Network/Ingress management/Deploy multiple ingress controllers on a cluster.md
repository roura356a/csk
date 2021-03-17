---
keyword: [deploy independent NGINX ingress controllers, Container Service for Kubernetes \(ACK\) clusters]
---

# Deploy multiple ingress controllers on a cluster

This topic describes how to deploy multiple independent NGINX ingress controllers on a cluster to provide different services for external users.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use SSH to connect to an ACK cluster.md)

The [Configure an ingress controller to use an internal SLB instance](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Configure an ingress controller to use an internal SLB instance.md) topic describes how to modify the default configurations of the NGINX ingress controller to use internal SLB instances. The two solutions described in the topic meet the needs of most scenarios. Assume that you have deployed multiple services on a cluster. This topic describes a solution that applies to scenarios where some services must be available to external users, but other services only allow requests from non-Kubernetes workloads in the same virtual private cloud \(VPC\). To meet such demands, you can deploy two independent NGINX ingress controllers and bind them to SLB instances of different network types.

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3836858951/p81390.png)

## Deploy a second NGINX ingress controller

You can perform the following steps to deploy an independent NGINX ingress controller to a cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **Alibaba Cloud Apps** tab, click **ack-ingress-nginx**.

4.  On the App Catalog - ack-ingress-nginx page, click the **Parameters** tab. In the **Deploy** section, specify a cluster, a namespace, and a release name, and click **Create**.

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |controller.image.repository|The image repository of ingress-nginx. If the cluster is deployed in a region outside China, we recommend that you set this parameter to the region ID.|
    |controller.image.tag|The image version of ingress-nginx. For more information, see [t1881583.md\#](/intl.en-US/Release notes/System Component change Records/Ingress-Nginx.md).|
    |controller.ingressClass|The ingress class of the ingress controller. The ingress controller handles only the ingress resources that are annotated with the ingress class. **Note:** In a cluster, the ingress class of each ingress controller must be unique. The ingress class of the default ingress controller in a cluster is nginx. Therefore, do not set this parameter to nginx. |
    |controller.replicaCount|The number of pod replicas of the ingress controller.|
    |controller.service.enabled|Specifies whether to use a public-facing SLB instance for load balancing. If you do not want to use a public-facing SLB instance, set the value to false.|
    |controller.service.internal.enabled|Specifies whether to use an internal SLB instance for load balancing. If you want to use an internal SLB instance, set this parameter to true.|
    |controller.kind|The deployment mode of the ingress controller. Valid values: Deployment and DaemonSet.|

    In the left-side navigation pane of the ACK console, click **Clusters**. On the Clusters page, find the cluster that you want to view, and click **Applications** in the **Actions** column. The details page of the cluster appears. In the left-side navigation pane, click **Releases**. On the **Helm** tab, view the newly deployed ingress controller.


## Test the connectivity

The following example deploys a test application and uses the newly deployed NGINX ingress controller to provide application services for external users.

1.  Deploy an NGINX application.

    ```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: nginx
    spec:
      replicas: 1
      selector:
        matchLabels:
          run: nginx
      template:
        metadata:
          labels:
            run: nginx
        spec:
          containers:
          - image: nginx
            imagePullPolicy: Always
            name: nginx
            ports:
            - containerPort: 80
              protocol: TCP
          restartPolicy: Always
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      sessionAffinity: None
      type: NodePort
    ```

2.  Use the NGINX ingress controller to provide application services for external users.

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: nginx
      annotations:
        # Set the value to the ingress class of the new NGINX ingress controller.
        kubernetes.io/ingress.class: "<YOUR_INGRESS_CLASS>"
    spec:
      rules:
      - host: foo.bar.com
        http:
          paths:
          - path: /
            backend:
              serviceName: nginx
              servicePort: 80
    ```

    **Note:** You must configure the kubernetes.io/ingress.class annotation.

    After you deploy the application, the ingress IP address is the same as the IP address of the new NGINX ingress controller.

    ```
     kubectl -n kube-system get svc nginx-ingress-lb
    ```

    ```
    NAME               TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
    nginx-ingress-lb   LoadBalancer   172.19.7.30   47.95.97.115   80:31429/TCP,443:32553/TCP   2d
    ```

    ```
    kubectl -n <YOUR_NAMESPACE> get svc nginx-ingress-lb
    ```

    ```
    NAME               TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
    nginx-ingress-lb   LoadBalancer   172.19.6.227   39.105.252.62   80:30969/TCP,443:31325/TCP   39m
    ```

    ```
    kubectl get ing
    ```

    ```
    NAME      HOSTS         ADDRESS         PORTS     AGE
    nginx     foo.bar.com   39.105.252.62   80        5m
    ```

3.  Access the application through the default and new NGINX ingress controllers.

    ```
     # Access the application through the default NGINX ingress controller. The 404 status code is expected.
      curl -H "Host: foo.bar.com" http://47.95.97.115
    default backend - 404                                                                                                                                                                                        
      # Access the application through the new NGINX ingress controller. The NGINX welcome page is expected. curl -H "Host: foo.bar.com" http://39.105.252.62
    <! DOCTYPE html>
    <html>
    <head>
    <title>Welcome to nginx! </title>
    <style>
        body {
            width: 35em;
            margin: 0 auto;
            font-family: Tahoma, Verdana, Arial, sans-serif;
        }
    </style>
    </head>
    <body>
    <h1>Welcome to nginx! </h1>
    <p>If you see this page, the nginx web server is successfully installed and
    working. Further configuration is required.</p>
    
    <p>For online documentation and support please refer to
    <a href="http://nginx.org/">nginx.org</a>.<br/>
    Commercial support is available at
    <a href="http://nginx.com/">nginx.com</a>.</p>
    
    <p><em>Thank you for using nginx.</em></p>
    </body>
    </html>
    ```


The preceding tests show that application services accessed through different NGINX ingress controllers do not interfere each other. This solution applies to scenarios where some services must be available to external users, but other services only allow requests from non-Kubernetes workloads in the same VPC.

[Deploy Ingresses in a high-reliability architecture](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Deploy a highly reliable Ingress controller.md)

[Configure an ingress controller to use an internal SLB instance](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Configure an ingress controller to use an internal SLB instance.md)

