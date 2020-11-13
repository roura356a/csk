# Use the service discovery feature based on Alibaba Cloud DNS PrivateZone in serverless Kubernetes clusters

Alibaba Cloud Serverless Kubernetes supports service discovery for intranet services, headless services, and clusterIP services.

-   Alibaba Cloud Domain Name System \(DNS\) PrivateZone is activated in the Alibaba Cloud DNS console.
-   A serverless Kubernetes \(ASK\) cluster is created. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).
-   The Kubernetes cluster is connected. For more information, see [Use kubectl to connect to an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to an ASK cluster.md).

Alibaba Cloud DNS PrivateZone is a private domain name resolution and management service based on Alibaba Cloud Virtual Private Cloud \(VPC\). You can map a private domain name to an IP address in one or more custom VPCs. Your private domain names are not accessible in other network environments.

1.  Create a deployment and services.

    Copy the following code into a YAML file and run the `kubectl create -f nginx-service.yaml` command to create a service:

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

2.  Run the following command to view the operating status of the created resources:

    ```
    kubectl get svc,pod,deployment
    ```

3.  Log on to the [Alibaba Cloud DNS console](https://dns.console.aliyun.com/).

4.  In the left-side navigation pane, click **PrivateZone**. The **All Zones** tab of the PrivateZone page appears.

    ![All Zones](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6246858951/p10241.png)

5.  In the zone list, find the target zone and click **Configure** in the Actions column. The Resolution Settings tab of the Resolution Settings page appears.

    ![](../images/p65338.png)

    **Note:** Each host record is in the `$svc.$ns` format and corresponds to a resolved IP address. The following resolution rules apply:

    -   LoadBalancer service: corresponds to only one resolution record in PrivateZone, which is the IP address of Server Load Balancer \(SLB\).
    -   ClusterIP service: corresponds to multiple resolution records in PrivateZone, which are the IP addresses of backend pods.
    -   Headless service: corresponds to multiple resolution records in PrivateZone, which are the IP addresses of backend pods.
    You can access a service by using the private domain name in the VPC. The private domain name can be a long domain name or a short domain name.

    -   Long domain name: You can use `$svc.$ns.svc.cluster.local.$clusterId` to connect to services in other clusters that are synchronized to PrivateZone.
    -   Short domain name: You can use `$svc` to connect to services under the same namespace and use `$svc.$ns` to connect to services in other namespaces.
    For more information, see [serverless-k8s-examples](https://github.com/AliyunContainerService/serverless-k8s-examples).


