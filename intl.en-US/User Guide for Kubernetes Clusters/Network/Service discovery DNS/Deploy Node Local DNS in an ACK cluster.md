---
keyword: [node-local-dns, DNS, service discovery, dnsConfig]
---

# Deploy Node Local DNS in an ACK cluster

Container Service for Kubernetes \(ACK\) allows you to deploy Node Local DNS to improve the stability and performance of service discovery. Node Local DNS runs a DNS caching agent as a DaemonSet on cluster nodes to improve the DNS performance. This topic describes how to deploy Node Local DNS for an application in an ACK cluster.

Before you deploy Node Local DNS in an ACK cluster, make sure that the following conditions are met:

-   A dedicated or managed Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A kubectl client is configured to communicate with the cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

## Introduction to Node Local DNS

The Node Local DNS component provided by ACK consists of an Admission Controller Deployment and a group of DaemonSets that run DNS caches.

-   The Admission Controller Deployment is used to intercept requests from pods and automatically inject pod dnsConfig information from the DNS cache.

    **Note:** If the Admission Controller Deployment is disabled, you must manually add DNS domain configurations to the pod configurations. For more information, see **Manually specify DNSConfig** on the **Description** tab in [Step 4](#step_310_r09_qbx).

-   The DaemonSets that run DNS caches can create a virtual network interface on each node. By default, the virtual network interface uses the IP address 169.254.20.10 to listen on requests. To change the IP address, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). Based on pod DNSConfig and node network settings, DNS requests generated in pods are proxied by using the DaemonSets that run DNS caches.

    **Note:** The DaemonSets that run DNS caches are built on CoreDNS and provide only proxy and cache services. Do not enable other features, such as Hosts and Rewrite, on these DaemonSets.


![Architecture diagram](../images/p199061.png "How Node Local DNS works")

|No.|Description|
|---|-----------|
|1|By default, pods to which the DNS local cache is injected use Node Local DNS to listen on query requests by using the IP address 169.254.20.10 on the node.|
|2|If Node Local DNS has no cache that responds to a query request, the node-local-upstream service is used to request CoreDNS for resolution.|
|3|CoreDNS uses the DNS server deployed in the virtual private cloud \(VPC\) to resolve non-cluster domain names.|
|4|When pods to which the DNS local cache is injected fail to connect to Node Local DNS, these pods use the kube-dns service to connect to CoreDNS for resolution as a backup solution.|
|5|Pods to which the DNS local cache is not injected use the kube-dns service to connect to CoreDNS for resolution.|

## Step 1: Install Node Local DNS

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **Alibaba Cloud Apps** tab, click **ack-node-local-dns**.

4.  On the **Description** tab, read the introduction to and parameter settings of **ack-node-local-dns**. Then, click the **Parameters** tab to configure the parameters.

    The parameters are described in the following table.

    |Parameter|Description|How to obtain the value|
    |---------|-----------|-----------------------|
    |upstream\_ip|The Cluster IP of the kube-upstream-ip service in the kube-system namespace. Node Local DNS uses the kube-upstream-ip service to communicate with CoreDNS and resolve cluster-local domain names.|You can obtain the service CIDR block of the cluster on the **Basic Information** tab and set the parameter to an IP address that is within the service CIDR block and is not used by other services. For more information about how to view basic information about a cluster, see [View basic information](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/View cluster information.md). **Note:** The upstream\_ip parameter must be set to an IP address that is within the service CIDR block of the cluster and is not used by other services. |
    |clusterDomain|The domain name of the cluster.|Query the --cluster-domain parameter of the kubelet process on a cluster node. The default value of the parameter is cluster.local.|

5.  In the **Deploy** panel on the right side of the page, select the cluster and namespace, specify a release name, and then click **Create**.


## Step 2: Enable Node Local DNS for an application

The following example shows how to enable Node Local DNS for a Deployment that is created in the default namespace.

1.  Run the following command to add a label to the namespace to which the Deployment belongs. In this example, the Deployment is created in the default namespace.

    ```
    kubectl label namespace default node-local-dns-injection=enabled
    ```

    **Note:** The admission controller ignores applications in the kube-system and kube-public namespaces. Do not configure to automatically inject dnsConfig to applications in these two namespaces.

2.  Deploy a sample application in the default namespace.

    1.  Use the following YAML template to create a sample application named ubuntu-deployment.

        ```
        apiVersion: apps/v1 # for versions before 1.8.0 use apps/v1beta1
        kind: Deployment
        metadata:
          name: ubuntu
          labels:
            app: ubuntu
        spec:
          replicas: 2
          selector:
            matchLabels:
              app: ubuntu
          template:
            metadata:
              labels:
                app: ubuntu
            spec:
              containers:
              - name: ubuntu
                image: ubuntu
                command: ["sh", "-c"]
                args: ["sleep 100000"]
        ```

    2.  Run the following command to deploy the application in the cluster:

        ```
        kubectl apply -f ubuntu-deployment.yaml
        ```

        Expected output:

        ```
        deployment.apps/ubuntu created
        ```

    3.  Run the following command to view the application information:

        ```
        kubectl get deployment ubuntu
        ```

        Expected output:

        ```
        NAME     READY   UP-TO-DATE   AVAILABLE   AGE
        ubuntu   2/2     2            2           7s
        ```

3.  Check whether dnsConfig is injected.

    1.  Run the following command to query pods:

        ```
        kubectl get pods
        ```

        Expected output:

        ```
        NAME                      READY   STATUS    RESTARTS   AGE
        ubuntu-766448f68c-mj8qk   1/1     Running   0          4m39s
        ubuntu-766448f68c-wf5hw   1/1     Running   0          4m39s
        ```

    2.  Run the following command to check whether Node Local DNS is enabled in the dnsConfig of a pod.

        Input:

        ```
        kubectl get pod ubuntu-766448f68c-mj8qk -o=jsonpath='{.spec.dnsConfig}'
        ```

        Expected output:

        ```
        map[nameservers:[169.254.20.10 172.21.0.10] options:[map[name:ndots value:5]] searches:[default.svc.cluster.local svc.cluster.local cluster.local]]
        ```

        The preceding output indicates that Node Local DNS is enabled for the application.

        After Node Local DNS is enabled for an application, the following parameters are added to the pods that are provisioned for the application. To ensure high availability of DNS services, the Cluster IP 172.21.0.10 of the kube-dns service is added to the nameservers parameter. This way, the kube-dns service is configured as a backup DNS service.

        ```
          dnsConfig:
            nameservers:
            - 169.254.20.10
            - 172.21.0.10
            options:
            - name: ndots
              value: "3"
            - name: attempts
              value: "2"
            - name: timeout
              value: "1"
            searches:
            - default.svc.cluster.local
            - svc.cluster.local
            - cluster.local
          dnsPolicy: None
        ```


## Troubleshooting

Before you enable Node Local DNS, make sure that the following conditions are met:

-   Newly created pods do not belong to the kube-system or kube-public namespace.
-   The namespace to which newly created pods belong has the `node-local-dns-injection=enabled` label.
-   The namespace to which newly created pods belong does not have labels that are related to Elastic Container Instance \(ECI\), such as `virtual-node-affinity-injection`, `eci`, and `alibabacloud.com/eci`.
-   Newly created pods do not have labels that are related to ECI, such as `eci` and `alibabacloud.com/eci`, or the `node-local-dns-injection=disabled` label.
-   Newly created pods have the `hostNetwork` network and the `ClusterFirstWithHostNet` DNSPolicy, or the pods do not have the `hostNetwork` network but have the `ClusterFirst` DNSPolicy.

**Related topics**  


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Overview.md)

[Introduction and configuration of the DNS service in ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Introduction and configuration of the DNS service in ACK clusters.md)

[Optimize DNS resolution for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Optimize DNS resolution for an ACK cluster.md)

