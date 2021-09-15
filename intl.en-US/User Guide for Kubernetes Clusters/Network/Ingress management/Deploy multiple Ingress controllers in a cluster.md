---
keyword: [deploy independent NGINX Ingress controllers, ACK clusters]
---

# Deploy multiple Ingress controllers in a cluster

This topic describes how to deploy multiple independent NGINX Ingress controllers in a Container Service for Kubernetes \(ACK\) cluster to provide different Internet-facing services.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A kubectl client is connected to the cluster. For more information, see [Connect to ACK clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

You can modify the configurations of the default NGINX Ingress controller to use internal-facing Server Load Balancer \(SLB\) instances. For more information about deploying NGINX Ingress controllers to meet the requirements of most scenarios, see [Configure an Ingress controller to use an internal-facing SLB instance](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Configure an Ingress controller to use an internal-facing SLB instance.md). This topic describes a solution that applies to scenarios where some services must be accessible to external users, but other services allow only requests from non-Kubernetes workloads in the same virtual private cloud \(VPC\). In this case, you can deploy two independent NGINX Ingress controllers and bind them to SLB instances of different network types.

![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3836858951/p81390.png)

## Deploy a new NGINX Ingress controller

After an ACK cluster is created, an NGINX Ingress controller with two pods is automatically deployed. An Internet-facing SLB instance is also created as the frontend load balancing Service.

You can perform the following steps to deploy another independent NGINX Ingress controller in the cluster:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **Alibaba Cloud Apps** tab, click **ack-ingress-nginx**.

4.  On the App Catalog - ack-ingress-nginx page, click the **Parameters** tab. In the **Deploy** section, specify the cluster, namespace, and release name, and then click **Create**.

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |controller.image.repository|The image repository of ingress-nginx. If the cluster is deployed in a region outside mainland China, we recommend that you set this parameter to the region ID.|
    |controller.image.tag|The image version of ingress-nginx. For more information, see [Nginx Ingress Controller](/intl.en-US/Release notes/System Component change Records/Networking/Nginx Ingress Controller.md).|
    |controller.ingressClass|The Ingress class of the Ingress controller. The Ingress controller handles only the Ingress resources that are annotated with the Ingress class. **Note:** The Ingress class of each Ingress controller must be unique in a cluster. The Ingress class of the default Ingress controller in a cluster is nginx. Therefore, do not set this parameter to nginx. |
    |controller.replicaCount|The number of pods of the Ingress controller.|
    |controller.service.enabled|Specifies whether to use an Internet-facing SLB instance for load balancing. If you do not want to use an Internet-facing SLB instance, set this parameter to false.|
    |controller.service.internal.enabled|Specifies whether to use an internal-facing SLB instance for load balancing. If you want to use an internal-facing SLB instance, set this parameter to true.|
    |controller.kind|The deployment mode of the Ingress controller. Valid values: Deployment and DaemonSet.|

5.  View the new NGINX Ingress controller

    1.  In the left-side navigation pane of the ACK console, click **Clusters**.

    2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    3.  In the left-side navigation pane of the cluster details page, choose **Applications** \> **Helm**.

        On the **Helm** page, you can find that the new NGINX Ingress controller is deployed.


## Test the connectivity

The following example deploys a test application and uses the newly deployed NGINX Ingress controller to provide Internet-facing services.

1.  Deploy an NGINX application.

    ```
    apiVersion: apps/v1
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

2.  Use the NGINX Ingress controller to provide Internet-facing services.

    ```
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: nginx
      annotations:
        # Set the value to the Ingress class of the new NGINX Ingress controller. 
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

    **Note:** You must configure the `kubernetes.io/ingress.class` annotation.

    After you deploy the application, perform the following steps to query the Ingress IP address and the IP address of the new NGINX Ingress controller:

    -   Run the following command to query the IP address of the Internet-facing SLB instance associated with the default NGINX Ingress controller:

        ```
         kubectl -n kube-system get svc nginx-ingress-lb
        ```

        Expected output:

        ```
        NAME               TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
        nginx-ingress-lb   LoadBalancer   172.19.7.30   47.95.97.115   80:31429/TCP,443:32553/TCP   2d
        ```

    -   Run the following command to query the IP address of the Internet-facing SLB instance associated with the new NGINX Ingress controller:

        ```
        kubectl -n <YOUR_NAMESPACE> get svc nginx-ingress-lb
        ```

        Expected output:

        ```
        NAME               TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
        nginx-ingress-lb   LoadBalancer   172.19.6.227   39.105.252.62   80:30969/TCP,443:31325/TCP   39m
        ```

    -   Run the following command to query the Ingress configurations:

        ```
        kubectl get ing
        ```

        Expected output:

        ```
        NAME      HOSTS         ADDRESS         PORTS     AGE
        nginx     foo.bar.com   39.105.252.62   80        5m
        ```

    The preceding output shows that the Ingress IP address is the same as the IP address of the new NGINX Ingress controller.

3.  Access the application through the default and new NGINX Ingress controllers.

    ```
     # Access the application through the default NGINX Ingress controller. The 404 status code is expected. 
      curl -H "Host: foo.bar.com" http://47.95.97.115
    default backend - 404                                                                                                                                                                                        
      # Access the application through the new NGINX Ingress controller. The NGINX welcome page is expected. curl -H "Host: foo.bar.com" http://39.105.252.62
    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to nginx!</title>
    <style>
        body {
            width: 35em;
            margin: 0 auto;
            font-family: Tahoma, Verdana, Arial, sans-serif;
        }
    </style>
    </head>
    <body>
    <h1>Welcome to nginx!</h1>
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


The preceding tests show that application services exposed through different NGINX Ingress controllers do not interfere each other. This solution is suitable for scenarios where some services must be accessible to external users while others allow only requests from non-Kubernetes workloads within the same VPC.

[Deploy Ingresses in a high-reliability architecture](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Deploy Ingresses in a high-reliability architecture.md)

[Configure an Ingress controller to use an internal-facing SLB instance](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Configure an Ingress controller to use an internal-facing SLB instance.md)

