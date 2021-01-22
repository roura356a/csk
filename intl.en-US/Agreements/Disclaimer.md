# Disclaimer

Container Service for Kubernetes \(ACK\) is committed to providing a stable and reliable enterprise-level container platform for its customers. However, Alibaba Cloud shall not be responsible for the following issues:

-   All damages and losses that are caused by the use of third-party or open source software other than that provided by Alibaba Cloud.
-   Improper operations, such as cluster upgrades through platforms other than ACK, Server Load Balancer \(SLB\) configuration changes through platforms other than ACK, and accidental resource deletion.
-   Critical configuration changes, such as modifications of the following folders and their paths, links, and content:
    -   /var/lib/kubelet
    -   /var/lib/docker
    -   /etc/kubernetes
    -   /etc/kubeadm
-   The use of system reserved annotations in templates, which results in resource unavailability, application failures, and exceptions.

    Labels prefixed with kubernetes.io/ or k8s.io/ are reserved for core components. Example: pv.kubernetes.io/bind-completed: "yes".

-   All losses that are caused by unstable connections to external Kubernetes clusters when you access the clusters by using the features of existing clusters in the ACK console.
-   All other improper operations that may affect the stability of clusters.

When you use registered clusters, Alibaba Cloud shall not be responsible for the following issues:

-   All losses that are caused by unstable connections to external Kubernetes clusters when you access the clusters by using the features of registered clusters in the ACK console.
-   Abnormal application status that is caused when you use registered clusters to manage labels, annotations, or tags of nodes in external clusters.
-   All other improper operations that may affect the stability of clusters.

When you use applications in App Catalog, Alibaba Cloud shall not be responsible for the following issues:

All damages and losses caused by the defects of open source software that you use to adapt and redevelop you applications.

