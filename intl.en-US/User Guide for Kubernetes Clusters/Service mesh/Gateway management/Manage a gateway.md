# Manage a gateway

This topic describes how to modify or delete a gateway in the Container Service console.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Deploy Istio](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Deploy Istio.md)
-   A gateway is deployed. For more information, see [Deploy a gateway](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Gateway management/Deploy a gateway.md).

## Modify a gateway

You can modify the gateway configuration by updating the YAML configuration.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  Click **View in YAML** in the Actions column for the target gateway.

    The YAML configuration similar to the following appears:

    ```
    apiVersion: networking.istio.io/v1alpha3
    kind: Gateway
    metadata:
      creationTimestamp: '2019-08-23T06:50:25Z'
      generation: 1
      name: my-gateway
      namespace: default
      resourceVersion: '15172143'
      selfLink: /apis/networking.istio.io/v1alpha3/namespaces/default/gateways/my-gateway
      uid: 492db126-c572-11e9-b65a-00163e13****
    spec:
      selector:
        istio: ingressgateway
      servers:
        - hosts:
            - '*'
          port:
            name: http
            number: 80
            protocol: HTTP
    ```

    -   `name` in metadata: the name of the gateway. In this example, the gateway name is my-gateway.
    -   `hosts`: the domain name of the gateway host.
    -   `name` in port: the name of the gateway port. In this example, the gateway port name is http.
    -   `number`: the port number of the gateway.
    -   `protocol`: the gateway protocol.
3.  After you modify the YAML configuration as required, click **Update**.


## Delete a gateway

1.  Click **Delete** in the Actions column for the target gateway.

2.  In the dialog box that appears, click **OK**. The gateway is deleted.


