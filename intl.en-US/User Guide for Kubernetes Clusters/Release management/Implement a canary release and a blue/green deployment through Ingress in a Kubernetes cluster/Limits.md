---
keyword: [limits, canary release]
---

# Limits

Container Service for Kubernetes \(ACK\) enables an Ingress to implement a canary release. This topic describes the limits of this feature.

To implement a canary release, make sure that the version of your Ingress controller is `0.12.0-5` or later.

To query the version of an Ingress controller, run one of the following commands:

-   If a service is deployed based on a Deployment, run the following command:

    ```
    kubectl -n kube-system get deploy nginx-ingress-controller -o yaml | grep -v 'apiVersion' | grep 'aliyun-ingress-controller'
    ```

-   If a service is deployed based on a DaemonSet, run the following command:

    ```
    kubectl -n kube-system get ds nginx-ingress-controller -o yaml | grep -v 'apiVersion' | grep 'aliyun-ingress-controller'
    ```


If the version of your Ingress controller is earlier than `0.12.0-5`, run one of the following commands to upgrade the controller:

-   If a service is deployed based on a Deployment, run the following command:

    ```
    kubectl -n kube-system set image deploy/nginx-ingress-controller nginx-ingress-controller=registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:0.12.0-5
    ```

-   If a service is deployed based on a DaemonSet, run the following command:

    ```
    kubectl -n kube-system set image ds/nginx-ingress-controller nginx-ingress-controller=registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:0.12.0-5
    ```


