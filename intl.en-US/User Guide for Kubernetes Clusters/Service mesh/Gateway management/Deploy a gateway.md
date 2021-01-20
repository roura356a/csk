# Deploy a gateway

This topic describes how to deploy a gateway.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Deploy Istio](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Deploy Istio.md)

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Service Mesh** \> **Gateways**.

3.  Select the cluster and the namespace that you want to manage and click **Create** in the upper-right corner.

4.  Configure parameters for the gateway and click **Create**. The gateway is deployed.

    ![Configure a gateway](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5012633061/p57266.png)

    Parameter description

    -   **Name**: the name of the gateway.
    -   **Service**: the proxy gateway. Default value: **istio-ingressgateway**. The proxy gateway and the gateway that you create must be deployed in the same namespace.
    -   **Ports**: the port that you can use to access the gateway. You can click **Add** to configure multiple ports for the gateway.
        -   Name: the port name.
        -   Protocol: the gateway protocol. Valid values: HTTP and TCP.
        -   Port: the port number. Valid values: 1 to 65535.
        -   Host: the gateway host. You can configure multiple hosts for the gateway.

On the Gateways page, you can view the gateway.

![Gateways](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6012633061/p57271.png)

