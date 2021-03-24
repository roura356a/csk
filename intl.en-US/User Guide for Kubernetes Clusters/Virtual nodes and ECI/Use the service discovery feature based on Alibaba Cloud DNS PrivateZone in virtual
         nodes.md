# Use the service discovery feature based on Alibaba Cloud DNS PrivateZone in virtual nodes

Virtual nodes of Alibaba Cloud Container Service for Kubernetes \(ACK\) support service discovery for intranet services, headless services, and ClusterIP services.

-   Alibaba Cloud DNS PrivateZone is activated. You can activate the service in the [Alibaba Cloud DNS console](https://dns.console.aliyun.com/).
-   A virtual node is created in an ACK cluster. For more information, see [Deploy ack-virtual-node in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md).
-   You have connected to the ACK cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

## Preparations

After you use a Helm chart to deploy a virtual node, confirm whether relevant environment variables are valid.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **Alibaba Cloud Apps** tab, click **ack-virtual-node**.

4.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. Click **ack-virtual-node**.

5.  On the App Catalog - ack-virtual-node page, click the **Parameters** tab to modify parameters, as shown in the following figure.

    ![App Catalog](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5246858951/p65159.png)

    -   `ECI_VPC`: The ID of the VPC connected to the ACK cluster. Replace it with the VPC ID of the current cluster.
    -   `ALIYUN_CLUSTERID`: The ID of the ACK cluster. Replace it with the ID of the current cluster. Required. You are not allowed to set the value to default.

## Procedure

1.  Create a deployment and a service.

    You can add the following sample code to a YAML file and run the `kubectl create -f nginx-service-ack.yaml` command to create a service:

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-headless-service
      annotations:
         service.beta.kubernetes.io/alibaba-cloud-private-zone-enable: "true"
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
      annotations:
         service.beta.kubernetes.io/alibaba-cloud-private-zone-enable: "true"
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
        service.beta.kubernetes.io/alibaba-cloud-private-zone-enable: "true"
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

    **Note:** By default, a service in an ACK cluster is not synchronized to Alibaba Cloud DNS PrivateZone. You must add the following annotation to the service YAML file to synchronize the resolution records of the service to Alibaba Cloud DNS PrivateZone:

    ```
      annotations:
         service.beta.kubernetes.io/alibaba-cloud-private-zone-enable: "true"
    ```

2.  Log on to the [Alibaba Cloud DNS console](https://dns.console.aliyun.com/).

3.  In the left-side navigation pane, click **PrivateZone**. On the **Hosted Zones** tab, you can view the number of records that are automatically generated in each zone.

    ![Hosted Zones](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7913693161/p10241.png)

4.  In the zone list, find the zone that you want to configure and click **Configure** in the Actions column. The Resolution Settings page appears.

    **Note:** Each resolution record is in the `$svc.$ns` format and corresponds to a resolved IP address. The following resolution rules apply:

    -   A LoadBalancer service corresponds to only one resolution record in Alibaba Cloud DNS PrivateZone, which is the IP address of the Server Load Balancer \(SLB\) instance.
    -   A ClusterIP service corresponds to multiple resolution records in Alibaba Cloud DNS PrivateZone, which are the IP addresses of backend pods.
    -   A headless service corresponds to multiple resolution records in Alibaba Cloud DNS PrivateZone, which are the IP addresses of backend pods.
    You can access a service by using the internal domain name in the VPC. The internal domain name can be a long domain name or a short domain name.

    -   Long domain name: You can use `$svc.$ns.svc.cluster.local.$clusterId` to connect to services that are synchronized from other clusters to Alibaba Cloud DNS PrivateZone.
    -   Short domain name: You can use `$svc` to connect to services in the same namespace and use `$svc.$ns` to connect to services in other namespaces.

