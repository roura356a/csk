# Kubernetes 1.16 release notes

Container Service for Kubernetes \(ACK\) strictly abides by the terms of the Certified Kubernetes Conformance Program. This topic describes the changes made to Kubernetes 1.16 by ACK.

## Upgrade description

ACK has upgraded the components of Kubernetes 1.16 to provide enhanced features.

|Core component|Version|Upgrade notes|
|--------------|-------|-------------|
|Kubernetes|1.16.9|Vulnerability CVE-2020-8555 is fixed in Kubernetes 1.16.9. For more information, see [Vulnerability fixed: CVE-2020-8555 in kube-controller-manager](/intl.en-US/Bulletin/Security bulletins/Vulnerability fixed: CVE-2020-8555 in kube-controller-manager.md).|
|1.16.6|In Kubernetes 1.16, the built-in CoreDNS version is V1.6.2. Compared with CoreDNS 1.3.1 in Kubernetes 1.14. the new version has the following changes: -   The proxy plug-in is replaced with the forward plug-in, which offers higher performance.
-   By default, the ready plug-in is enabled. It is used to check the readiness of containers.

The Corefile will be automatically migrated to match the new CoreDNS version. |
|Docker|19.03.5 \(containerd 1.2.10\)|None|
|etcd|3.4.3|None|

## Version description

-   **Performance optimizations**

    Compared with Kubernetes 1.14, Kubernetes 1.16.6 has the following performance optimizations.

    -   Optimizes PodAffinity to improve performance by 100%.
    -   Optimizes serialization operations. Improves the performance of the pod list operation by 40%. Improves the performance of the node list operation by 30%.
    -   Improves the performance of processing apply requests that involve large map objects on the server side.
    -   Optimizes the heartbeat solution based on node leases. Reduces the number of lease queries per minute that are sent to the API server or etcd by 50,000 for a cluster of 8,000 nodes.
    -   Significantly speeds up pod creation. In terms of the creation of stateless pods \(does not involve mounting volumes such as ConfigMaps and Secrets\):
        -   Both Kubernetes 1.16.6 and 1.14 meet the service-level agreement \(SLA\) defined by Special Interest Group \(SIG\) Scalability. If images are pulled, 99% of pods can be started within 5 seconds.
        -   Based on the statistics of the 1% of pod creation processes that require the longest time, Kubernetes 1.14 requires nearly 5 seconds to create a pod whereas Kubernetes 1.16.6 requires only 3 seconds under the same conditions.
    Compared with previous versions, Docker 19.03.5 has the following optimizations:

    -   The built-in buildkit speeds up image builds.
    -   The runC runtime optimizes systemd detection logic. Containers start faster and occupy less memory.
    Docker 19.03.5 has the following improvements on runtime stability:

    -   Fixes the issue where pods occasionally restart when health checks are performed by the exec commands.
    -   Fixes vulnerability CVE-2018-15664, which is exposed by the docker cp command.
    -   Fixes the issue where Docker does not respond when a rich container that runs multiple processes exits.
    -   Fixes the handle leak issue in containerd.
-   **Feature enhancements**

    Compared with Kubernetes 1.14, Kubernetes 1.16.6 has the following important changes.

    -   By default, the following API versions are not supported: extensions/v1beta1, apps/v1beta1, and apps/v1beta2. apps/v1beta1 and all resources defined in apps/v1beta1 are replaced by apps/v1. The DaemonSets, Deployments, and ReplicaSets defined in extensions/v1beta1 are replaced by apps/v1. The NetworkPolicies defined in extensions/v1beta1 are replaced by networking.k8s.io/v1.

        **Note:** To ensure compatibility with your workloads, ACK has added support for the preceding API versions in Kubernetes 1.16.6 and will end the support in Kubernetes 1.18. We recommend that you change the API versions as soon as possible.

    -   The following kubelet security control parameters are deprecated and removed: AllowPrivileged, HostNetworkSources, HostPIDSources, and HostIPCSources. Access control parameters such as PodSecurityPolicy are added for enhanced security.
    -   More features have been stabilized. For example, CustomResourceDefinitions \(CRDs\) and admission webhooks are in general availability.

## Enhancements

ACK has enhanced Kubernetes 1.16 in the following aspects:

-   Enhanced stability and performance
    -   Adds retries for idempotent functions to improve the success rate of cluster creation.
    -   Existing containers are not restarted during kubelet upgrades.
    -   Fixes kubelet startup failures caused by hugetlb.
-   Improved observability
    -   Optimizes logs of liveness probes that are sent from Server Load Balancer \(SLB\) instances to apiserver.
    -   Adjusts the log level of aggregationcontroller.
    -   Optimizes the output of the get cs command in managed Kubernetes clusters.
    -   Optimizes metrics of sandboxed containers based on the Summary API and container API.

