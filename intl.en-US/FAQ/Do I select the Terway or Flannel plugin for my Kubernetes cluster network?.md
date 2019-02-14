# Do I select the Terway or Flannel plugin for my Kubernetes cluster network? {#concept_qsd_ckm_q2b .concept}

This topic describes Terway and Flannel, two network plugins provided by Container Service for creating a Kubernetes cluster. This information helps you select an appropriate network plugin when creating a cluster.

When you create a Kubernetes cluster, Alibaba Cloud Container Service provides two network plugins: Terway and Flannel.

-   Flannel: a simple and stable community [Flannel](https://github.com/coreos/flannel) CNI plugin. Flannel works with the high-speed network of Alibaba Cloud VPC, providing a high-performance and stable container network for clusters. However, it provides only a few simple features. For example, it does not support the Kubernetes Network Policy.
-   Terway: a network plugin developed by Alibaba Cloud Container service. It is fully compatible with Flannel, and can allocate Alibaba Cloud Elastic Network Interfaces \(ENIs\) to containers. It can also define the access policies between containers according to the Kubernetes Network Policy. In addition, it supports bandwidth limiting for individual containers. If you do not need to use a Network Policy, we recommend that you select Flannel. In other cases, we recommend that you select Terway. For more information, see [Terway network plugin](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Network management/Terway network plugin.md#).

