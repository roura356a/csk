# Canary release limits

This topic describes the limits for a canary release that is implemented by the Ingress function provided by Alibaba Cloud Container Service for Kubernetes.

The Ingress controller of Alibaba Cloud Container Service for Kubernetes must be V `0.12.0-5` or later.

To view the version number of the Ingress controller, run either of the following commands as required.

-   For a cluster in which applications are deployed by using the Deployment method, run:

    ```
    kubectl -n kube-system get deploy nginx-ingress-controller -o yaml | grep -v 'apiVersion' | grep 'aliyun-ingress-controller'
    ```

-   For a cluster in which applications are deployed by using the DaemonSet method, run:

    ```
    kubectl -n kube-system get ds nginx-ingress-controller -o yaml | grep -v 'apiVersion' | grep 'aliyun-ingress-controller'
    ```


If your Ingress Controller is earlier than `0.12.0-5`, you can upgrade it by running either of the following commands as required.

-   For a cluster in which applications are deployed by using the Deployment method, run:

    ```
    kubectl -n kube-system set image deploy/nginx-ingress-controller nginx-ingress-controller=registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:0.12.0-5
    ```

-   For a cluster in which applications are deployed by using the DaemonSet method, run:

    ```
    kubectl -n kube-system set image ds/nginx-ingress-controller nginx-ingress-controller=registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:0.12.0-5
    ```


