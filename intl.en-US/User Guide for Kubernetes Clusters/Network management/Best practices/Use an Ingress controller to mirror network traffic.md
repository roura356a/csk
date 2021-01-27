---
keyword: [Ingress controller, traffic mirroring, simulation]
---

# Use an Ingress controller to mirror network traffic

Container Service for Kubernetes \(ACK\) allows you to mirror network traffic to different clusters. Traffic mirroring is used in system simulations and troubleshooting. This topic describes how to use an Ingress controller to mirror network traffic.

Two ACK clusters are created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md). In this topic, a cluster named K8s Product Cluster is used as the production environment and another cluster named K8s Stage Cluster is used as the staging environment.

## Scenarios

Traffic mirroring can be used in the following scenarios:

-   Major changes are to be made to a system or new features are to be released. In this case, you must run stress tests to evaluate the stability of the system. Production workloads are usually simulated in a staging environment to test the stability of a new system before the system is released. However, the actual loads are difficult to estimate because the system may receive both normal and abnormal network traffic. To address this issue, you can mirror network traffic from applications that are deployed in the production environment to the staging environment. Then, you can simulate the production workloads in the staging environment.
-   When a system deployed in the production environment encounters a performance bottleneck and you cannot locate the cause, you can mirror the network traffic of the system to a staging environment. This way, you can troubleshoot errors in the staging environment.

    ![Scenarios](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6719371161/p210897.png)


## Step 1: Deploy basic applications

1.  Deploy applications in the cluster named K8s Product Cluster and check whether the applications are accessible.

    1.  Deploy an application in the cluster named K8s Product Cluster and use an Ingress to expose the Services in the cluster.

        ```
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: nginx-deployment
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: nginx
          template:
            metadata:
              labels:
                app: nginx
            spec:
              containers:
              # The previous version of the image is deployed.
              - image: registry.cn-hangzhou.aliyuncs.com/xianlu/old-nginx
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
          name: nginx-service
        spec:
          ports:
          - port: 80
            protocol: TCP
            targetPort: 80
          selector:
            app: nginx
          type: NodePort
        ---
        apiVersion: extensions/v1beta1
        kind: Ingress
        metadata:
          name: nginx-ingress
        spec:
          rules:
          # By default, the application is deployed in the region of the cluster. You can specify a region and configure DNS resolution.
          - host: nginx.c37bf6b77bded43669ba2fb67448b****.cn-hangzhou.alicontainer.com
            http:
              paths:
              - path: /
                backend:
                  serviceName: nginx-service
                  servicePort: 80
        ```

    2.  Run the following command to view the Ingress settings:

        ```
        kubectl get ing nginx-ingress
        ```

        Expected output:

        ```
        NAME            HOSTS                                                                  ADDRESS         PORTS     AGE
        nginx-ingress   nginx.c37bf6b77bded43669ba2fb67448b****.cn-hangzhou.alicontainer.com   47.110.1**.**   80        8m
        ```

    3.  Run the following command to check whether you can access the domain name of the application:

        ```
        curl http://nginx.c37bf6b77bded43669ba2fb67448b****.cn-hangzhou.alicontainer.com
        ```

2.  Deploy the same application in the cluster named K8s Stage Cluster and check whether the application is accessible.

    1.  Deploy the same application in the cluster named K8s Stage Cluster and use an Ingress to expose the Services in the cluster.

        ```
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: nginx-deployment
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: nginx
          template:
            metadata:
              labels:
                app: nginx
            spec:
              containers:
              # The latest version of the image is deployed.
              - image: registry.cn-hangzhou.aliyuncs.com/xianlu/new-nginx
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
          name: nginx-service
        spec:
          ports:
          - port: 80
            protocol: TCP
            targetPort: 80
          selector:
            app: nginx
          type: NodePort
        ---
        apiVersion: extensions/v1beta1
        kind: Ingress
        metadata:
          name: nginx-ingress
        spec:
          rules:
          # By default, the application is deployed in the region of the cluster. You can specify a region and configure DNS resolution.
          - host: nginx.c41eb6ca34a3e49f7aea63b8bc9e8****.cn-beijing.alicontainer.com
            http:
              paths:
              - path: /
                backend:
                  serviceName: nginx-service
                  servicePort: 80
        ```

    2.  Run the following command to view the Ingress settings:

        ```
        kubectl get ing nginx-ingress
        ```

        Expected output:

        ```
        NAME            HOSTS                                                                 ADDRESS        PORTS     AGE
        nginx-ingress   nginx.c41eb6ca34a3e49f7aea63b8bc9e8****.cn-beijing.alicontainer.com   39.106. ***.*   80        1m
        ```

    3.  Run the following command to check whether you can access the domain name of the application:

        ```
        curl http://nginx.c41eb6ca34a3e49f7aea63b8bc9e8****.cn-beijing.alicontainer.com
        ```


## Step 2: Mirror the network traffic

Mirror all network traffic of the application deployed in the cluster K8s Product Cluster to Services in the cluster K8s Stage Cluster. This way, all requests that are sent to the domain name `nginx.c37bf6b77bded43669ba2fb67448b****.cn-hangzhou.alicontainer.com` are replicated to the domain name `nginx.c41eb6ca34a3e49f7aea63b8bc9e8****.cn-beijing.alicontainer.com`.

**Note:** The Ingress of the cluster K8s Stage Cluster functions only as a receptor to receive the mirrored network traffic. Do not modify the settings of the Ingress in the cluster K8s Stage Cluster. You can modify the Ingress of the cluster K8s Product Cluster.

![Mirror network traffic](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3599371161/p210900.png)

1.  Run the following command to modify nginx-configuration. Set the percentage of network traffic that you want to mirror and specify the domain name that receives the mirrored network traffic:

    ```
    kubectl -n kube-system edit cm nginx-configuration
    ```

    Add the following content to nginx-configuration:

    ```
    http-snippet: |
       split_clients "$date_gmt" $mirror_servers {
          100%    nginx.c41eb6ca34a3e49f7aea63b8bc9e8****.cn-beijing.alicontainer.com;
       }
    
    # Notes on configurations:
    # 1. The percentage value of network traffic that is mirrored must be greater than 0 and can be at most 100. The sum of percentage values cannot exceed 100.
    # 2. You can set one or more applications that receive the mirrored network traffic.
    ```

2.  Run the following command to modify nginx-ingress:

    You can add traffic mirroring settings to the source Ingress by setting the configuration-snippet and server-snippet annotations.

    ```
    kubectl  edit ingress nginx-ingress
    ```

    Modify `configuration-snippet` and `server-snippet` of the nginx-ingress controller based on your business requirements. The following YAML file is an example:

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: nginx-ingress
      annotations:
        nginx.ingress.kubernetes.io/configuration-snippet: |
            mirror /mirror; # You can repeat this setting N times to mirror the network traffic N times.
        nginx.ingress.kubernetes.io/server-snippet: |
            location = /mirror {
                internal;
                # Does not print the log of mirrored requests
                #access_log off; 
                # Set proxy_upstream_name in the following format: [Namespace]-[BackendServiceName]-[BackendServicePort]
                set $proxy_upstream_name    "default-nginx-service-80";
                # A custom string that is carried in the request header X-Shadow-Service and then passed to the mirror server.
                set $shadow_service_name    "nginx-product-service"; 
                proxy_set_header X-Shadow-Service  $shadow_service_name;
                proxy_set_header Host $mirror_servers;
                proxy_pass http://$mirror_servers$request_uri;
            }
    spec:
      rules:
      - host: nginx.c37bf6b77bded43669ba2fb67448b****.cn-hangzhou.alicontainer.com
        http:
          paths:
          - path: /
            backend:
              serviceName: nginx-service
              servicePort: 80
    ```


## Check the result

Access the domain name `nginx.c37bf6b77bded43669ba2fb67448b****.cn-hangzhou.alicontainer.com`. This domain name belongs to the application that is deployed in the cluster K8S Product Cluster. Run the following command. The result shows that each request sent to the domain name of the application deployed in the cluster K8S Product Cluster is replicated, and then sent to the application deployed in the cluster K8S Stage Cluster.

```
kubectl -n kube-system logs --tail-o -f nginx-ingress-controller-674c96ffbc-9mc8n
```

![Result of traffic mirroring](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6719371161/p211191.png)

