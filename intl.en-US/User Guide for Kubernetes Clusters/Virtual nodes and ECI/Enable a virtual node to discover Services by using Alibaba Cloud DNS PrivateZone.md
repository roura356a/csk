# Enable a virtual node to discover Services by using Alibaba Cloud DNS PrivateZone

You can enable virtual nodes of Container Service for Kubernetes \(ACK\) to discover the following Services: intranet Services, headless Services, and ClusterIP Services.

-   Alibaba Cloud DNS PrivateZone is activated. You can activate the service in the [Alibaba Cloud DNS console](https://dns.console.aliyun.com/).
-   A virtual node is created in an ACK cluster. For more information, see [Deploy the virtual node controller and use it to create Elastic Container Instance-based pods](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md).
-   You have connected to the ACK cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

## Prerequisites

After you use a Helm chart to deploy a virtual node, verify that the relevant environment variables are valid.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **Alibaba Cloud Apps** tab, click **ack-virtual-node**.

4.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog** and then click **ack-virtual-node**.

5.  On the App Catalog - ack-virtual-node page, click the **Parameters** tab to modify the parameters, as shown in the following figure.

    ![App Catalog](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5246858951/p65159.png)

    -   `ECI_VPC`: The ID of the virtual private cloud \(VPC\) where the ACK cluster is deployed. Replace it with the VPC ID of the current cluster.
    -   `ALIYUN_CLUSTERID`: The ID of the ACK cluster. Replace it with the ID of the current cluster. You are not allowed to set the value to default or leave this parameter empty.

## Procedure

1.  Create a Deployment and Services.

    You can add the following sample code to a YAML file and run the `kubectl create -f nginx-service-ack.yaml` command to create a Deployment and Services:

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

    **Note:** By default, the DNS records of Services in an ACK cluster are not synchronized to Alibaba Cloud DNS PrivateZone. To synchronize these records, you must add the following annotation to the Service YAML file:

    ```
      annotations:
         service.beta.kubernetes.io/alibaba-cloud-private-zone-enable: "true"
    ```

    This way, the virtual node Controller synchronizes the DNS records of the Services to Alibaba Cloud DNS PrivateZone.

2.  Log on to the [Alibaba Cloud DNS console](https://dns.console.aliyun.com/).

3.  In the left-side navigation pane, click **PrivateZone**. On the **Hosted Zones** tab, you can view the PrivateZone records that are automatically generated in each zone.

    ![List of Hosted Zones](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7913693161/p10241.png)

4.  In the list of Hosted Zones, find the zone that you want to configure and click **Configure** in the Actions column. The Resolution Settings page appears.

    ![Resolution Settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0487558161/p65338.png)

    **Note:** All PrivateZone records are in the `$svc.$ns` format. Each PrivateZone record maps a Service to an IP address. The following resolution rules apply:

    -   A LoadBalancer Service corresponds to only one PrivateZone record. The record maps the Service to the IP address of the Server Load Balancer \(SLB\) instance.
    -   A ClusterIP Service corresponds to multiple PrivateZone records. The records map the Service to the IP addresses of backend pods.
    -   A headless Service corresponds to multiple PrivateZone records. The records map the Service to the IP addresses of backend pods.
    You can access a Service within the VPC through the private domain name of the Service.

    -   You can use `$svc.$ns.svc.cluster.local.$clusterId` to access Services that are discovered in the current cluster. You can also use the long domain name to access Services in other clusters after you use Alibaba Cloud DNS PrivateZone to discover these Services.
    -   You can use `$svc` to access Services in the current namespace and use `$svc.$ns` to access Services in other namespaces.

