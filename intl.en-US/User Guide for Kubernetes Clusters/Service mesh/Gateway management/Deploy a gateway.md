# Deploy a gateway

This topic describes how to deploy a gateway.

-   -   
1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane under Container Service - Kubernetes, choose **Service Mesh** \> **Gateways**. The Gateways page appears on the right.

3.  Select the cluster and namespace, and click **Create** in the upper-right corner.

4.  Set the gateway parameters and click **Create**. The gateway is deployed.

    Parameter descriptions:

    -   **Name**: the name of the gateway to deploy.
    -   **Service**: the gateway proxy. The default proxy is **istio-ingressgateway**. The proxy must be in the same namespace as the gateway to deploy.
    -   **Ports**: the gateway port. You can click **Add** to configure multiple ports for the gateway.
        -   Name: the port name.
        -   Protocol: the gateway protocol. Valid values: HTTP and TCP.
        -   Port: the port number. Valid values: 1 to 65535.
        -   Host: the gateway host. You can add multiple hosts for the gateway.

You can find the gateway on the Gateways page.

