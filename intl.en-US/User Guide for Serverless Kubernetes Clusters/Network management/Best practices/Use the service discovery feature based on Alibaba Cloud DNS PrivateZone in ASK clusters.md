---
keyword: [service discovery, Alibaba Cloud DNS PrivateZone]
---

# Use the service discovery feature based on Alibaba Cloud DNS PrivateZone in ASK clusters

Serverless Kubernetes \(ASK\) clusters support service discovery for intranet Services, headless Services, and ClusterIP Services.

-   Alibaba Cloud DNS PrivateZone is activated. You can activate the service in the [Alibaba Cloud DNS console](https://dns.console.aliyun.com/).
-   [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).
-   You are connected to the ASK cluster. For more information, see [Use kubectl to connect to an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster/Manage and access clusters/Use kubectl to connect to an ASK cluster.md).

Alibaba Cloud DNS PrivateZone is a private domain name resolution and management service based on Virtual Private Cloud \(VPC\). You can map a private domain name to an IP address in one or more VPCs that you create. Your private domain names are not accessible in other network environments.

1.  Create a Deployment and Services.

    You can add the following sample code to a YAML file and run the `kubectl create -f nginx-service-ack.yaml` command to create a Deployment and Services.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-headless-service
    spec:
      ports:
      - port: 80
        protocol: TCP
      selector:
        app: nginx
      clusterIP: None
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-clusterip-service
    spec:
      ports:
      - port: 80
        protocol: TCP
      selector:
        app: nginx
      type: ClusterIP
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-intranet-service
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-address-type: intranet
    spec:
      ports:
      - port: 80
        protocol: TCP
      selector:
        app: nginx
      type: LoadBalancer
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-deployment
      labels:
        app: nginx
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image:  nginx:alpine
            ports:
            - containerPort: 80
    ```

2.  Run the following command to view the state of the created application:

    ```
    kubectl get svc,pod,deployment
    ```

3.  Log on to the [Alibaba Cloud DNS console](https://dns.console.aliyun.com/).

4.  In the left-side navigation pane, click **PrivateZone**. On the page that appears, click the **Hosted Zones** tab.

5.  Select the zone that you want to manage and click **Configure** in the **Actions** column.

    **Note:** All PrivateZone records are in the `$svc.$ns` format. Each PrivateZone record maps a Service to an IP address. The following resolution rules apply:

    -   A LoadBalancer Service corresponds to only one PrivateZone record. The record maps the Service to the IP address of the Server Load Balancer \(SLB\) instance.
    -   A ClusterIP Service corresponds to only one PrivateZone record. The record maps the Service to the IP address of the cluster.
    -   A headless Service corresponds to multiple PrivateZone records. The records map the Service to the IP addresses of backend pods.
    You can access a Service within the VPC through the private domain name of the Service.

    -   You can use `$svc.$ns.svc.cluster.local.$clusterId` to access Services that are discovered in the current cluster. You can also use the long domain name to access Services in other clusters after you use Alibaba Cloud DNS PrivateZone to discover these Services.
    -   You can use `$svc` to access Services in the current namespace and use `$svc.$ns` to access Services in other namespaces.
    For more information, see [serverless-k8s-examples](https://github.com/AliyunContainerService/serverless-k8s-examples).


