# How to select a Kubernetes cluster network plug-in {#concept_qsd_ckm_q2b .concept}

Container Services provides two types of network plug-ins for creating a Kubernetes cluster: Flannel and Terway. To select a Kubernetes cluster network plug-in, see the following introduction of the two types of plug-ins:

-   Flannel: This network plug-in uses the simple and stable [Flannel](https://github.com/coreos/flannel) cni plug-in. Combined with the high speed network of Alibaba Cloud Virtual Private Cloud \(VPC\), this plug-in provides the cluster with high-performance and stable container network experience. The function of this plug-in is simple and it supports only a few of features. Currently, we recommend that you select this network plug-in.
-   Terway: This network plug-in is a self- developed network plugin for Alibaba Cloud Container Service. By using this plug-in, you can allocate elastic network cards of Alibaba Cloud to containers, use network policies of Kubernetes to define access polices for containers, and limit the bandwidth traffic for a single container. This plug-in is currently in the beta phase. You can use this plug-in in a testing environment or use it to experience the preceding new features.
    -   Using an elastic network card:

        Bind a worker node with an elastic network card in advance and reboot the node. Configure `k8s.aliyun.com/eni: "true"` in `annotation` of the pod. Then the pre-bound elastic card is used when a container is created.

    -   Use a network policy:

        See [https://kubernetes.io/docs/concepts/services-networking/network-policies/](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

    -   Limit pod bandwidth:

        In the annotation of the pod, you can use `k8s.aliyun.com/ingress-bandwidth` and `k8s.aliyun.com/egress-bandwidth` to specify the maximum ingress bandwidth and egress bandwidth for the pod, respectively. For example, `k8s.aliyun.com/ingress-bandwidth: 1m` and `k8s.aliyun.com/egress-bandwidth: 1m`.


