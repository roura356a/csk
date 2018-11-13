# Do I select the Terway or Flannel plug-in for my Kubernetes cluster network? {#concept_qsd_ckm_q2b .concept}

This document describes Terway and Flannel, two network plug-ins provided by Container Service for creating a Kubernetes cluster. This information should help you select an appropriate network plug-in when creating a cluster.

Container Service provides a stable and high-performance container network by deeply integrating the Kubernetes network and Alibaba Cloud Virtual Private Cloud \(VPC\). Container Service supports the following types of interconnections:

-   A pod accesses another pod in the same container cluster.
-   A pod accesses a service in the same container cluster.
-   Elastic Compute Service \(ECS\) accesses a service in the same container cluster.
-   A pod directly accesses ECS \(\*\) in the same VPC.
-   ECS directly accesses a pod \(\*\) in the same VPC.

**Note:** The asterisk \(\*\) indicates that a security group rule must be set correctly.

When you create a Kubernetes cluster, Alibaba Cloud Container Service provides two network plug-ins: Terway and Flannel.

-   Flannel: a simple and stable community [Flannel](https://github.com/coreos/flannel) CNI plug-in. Flannel works with the high-speed network of Alibaba Cloud VPC, providing a high-performance and stable container network for clusters. However, it provides only a few simple features. For example, it does not support the Kubernetes Network Policy.
-   Terway: a network plug-in developed by Alibaba Cloud Container service. It is fully compatible with Flannel, and can allocate Alibaba Cloud Elastic Network Interfaces \(ENIs\) to containers. It can also define the access policies between containers according to the Kubernetes Network Policy. In addition, it supports bandwidth limiting for individual containers. If you do not need to use a Network Policy, we recommend that you select Flannel. In other cases, we recommend that you select Terway.
    -   Use an ENI.

        Bind an ENI to a Worker node in advance and restart the node. In `annotation` of a pod, configure `k8s.aliyun.com/eni: "true"`. Then you can use the bound ENI when you create a container.

    -   Use a Network Policy.

        For details, see [https://kubernetes.io/docs/concepts/services-networking/network-policies/](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

        The Network Policy of Terway is implemented by integrating Calico Felix. Therefore, the Network Policy and Calico have the same functions. Users who built their own clusters to use Calico can turn to Container Service Kubernetes through Terway.

        The current version of Felix integrated by Terway is 2. 6.6.

    -   Limit pod bandwidth.

        In annotation of a pod, you can use `k8s.aliyun.com/ingress-bandwidth` and `k8s.aliyun.com/egress-bandwidth` to specify the maximum ingress bandwidth and egress bandwidth for the pod respectively, for example, `k8s.aliyun.com/ingress-bandwidth: 1m` and `k8s.aliyun.com/egress-bandwidth: 1m`.


