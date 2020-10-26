# Bind a virtual service to a gateway

This topic describes how to bind a virtual service to a gateway.

-   A Kubernetes cluster is created with ACK. For more information, see [t16639.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Proprietary Edition.md).
-   Istio is deployed on the Kubernetes cluster. For more information, see [t20172.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Deploy Istio.md).
-   A virtual service is created.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane under Container Service - Kubernetes, choose **Service Mesh** \> **Virtual Services**. The Virtual Services page appears.

    **Note:** In the **Gateways** column, the **Bind** button is available for virtual services that are not bound to gateways.

3.  In the Gateways column, click **Bind** for the target virtual service. The Add page appears.

4.  Select a gateway from the Name drop-down list and click **OK**. The virtual service is bound to the gateway.


