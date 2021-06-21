# Use the service discovery feature based on Alibaba Cloud DNS PrivateZone in ASK clusters

Serverless Kubernetes \(ASK\) clusters support service discovery for intranet Services, headless Services, and ClusterIP type Services.

-   Alibaba Cloud DNS PrivateZone is activated in the Alibaba Cloud DNS console.
-   [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).
-   You are connected to the ASK cluster. For more information, see [Use kubectl to connect to an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster/Manage and access clusters/Use kubectl to connect to an ASK cluster.md).

Alibaba Cloud DNS PrivateZone is a private domain name resolution and management service based on Virtual Private Cloud \(VPC\). You can map a private domain name to an IP address in one or more custom VPCs. Your private domain names are not accessible in other network environments.

1.  Create a Deployment and a Service.

    You can copy the following content into a YAML file and run the `kubectl create -f nginx-service-ack.yaml` command to create a Deployment and a Service.

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

4.  In the left-side navigation pane, click **PrivateZone** to go to the **All Zones** tab.

5.  On the All Zones tab, find the zone that you want to manage and click **Configure** in the Actions column. The resolution settings page appears.

    **Note:** Each record is in the `$svc.$ns` format and corresponds to an IP address. The following resolution rules apply:

    -   A LoadBalancer type Service corresponds to only one resolution record in Alibaba Cloud DNS PrivateZone. The record corresponds to the IP address of the related Server Load Balancer \(SLB\) instance.
    -   A ClusterIP type Service corresponds to only one resolution record in Alibaba Cloud DNS PrivateZone. The record corresponds to the IP address of the cluster.
    -   A headless Service corresponds to multiple resolution records in Alibaba Cloud DNS PrivateZone. These records correspond to the IP addresses of the backend pods.
    You can access a Service by using the private domain name in the VPC.

    -   Long domain name: You can use a long domain name of the `$svc.$ns.svc.cluster.local.$clusterId` format to access Services that are synchronized from other clusters to Alibaba Cloud DNS PrivateZone.
    -   Short domain name: You can use a short domain name of the `$svc` format to access Services in the same namespace. You can use a short domain name of the `$svc.$ns` format to access Services in other namespaces.
    For more information, see [serverless-k8s-examples](https://github.com/AliyunContainerService/serverless-k8s-examples).


