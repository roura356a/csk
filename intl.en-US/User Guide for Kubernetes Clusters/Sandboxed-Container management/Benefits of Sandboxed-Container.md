---
keyword: [benefits, scenarios, Sandboxed-Container]
---

# Benefits of Sandboxed-Container

This topic describes the advantages and applicable scenarios of Sandboxed-Container and compares Sandboxed-Container with open source Kata Containers. This allows you to learn more about the benefits of Sandboxed-Container.

Sandboxed-Container is an alternative to the Docker runtime environment. It supports the following features:

-   Sandboxed-Container allows your applications to run in a sandboxed and lightweight virtual machine. This virtual machine is equipped with a dedicated kernel and provides better isolation and enhanced security.

-   Compared with open source Kata Containers, Sandboxed-Container is optimized in terms of storage, networking, and stability.

-   You can use Sandboxed-Container to isolate untrusted applications and applications of different tenants for higher security. You can also use Sandboxed-Container to isolate applications with faults and applications with degraded performance. This minimizes the negative impact on your service. In addition, Sandboxed-Container offers the same user experience as Docker in terms of logging, monitoring, and elastic scaling.


## Benefits

Compared with Docker, Sandboxed-Container has the following benefits:

-   Strong isolation based on sandboxed and lightweight virtual machines.
-   Network Attached Storage \(NAS\) file systems and disks can be mounted to Sandboxed-Container. This provides the same storage performance as storage volumes that are mounted to the host.
-   Compatibility with runC in terms of application management.
-   High performance: up to 90% performance of runC.
-   The same user experience as runC in terms of logging, monitoring, and storage.
-   Supports RuntimeClass.
-   Fewer requirements on technical expertise and skills of using virtual machines.
-   Higher stability than Kata Containers.

## Comparison between Sandboxed-Container and Kata Containers

Sandboxed-Container outperforms Kata Containers in the following aspects.

|Item|Category|Sandboxed-Container|Kata Containers|
|----|--------|-------------------|---------------|
|Sandbox startup time consumption|About 150 ms|About 500 ms|
|Additional overhead of sandboxes|Low|High|
|Root file system|virtio-fs. Performance: ☆☆☆☆|9pfs. Performance: ☆|
|Volume|HostPath/EmptyDir|virtio-fs. Performance: ☆☆☆☆|
|Disks with block storage|-   virtio-fs \(default\). Performance: ☆☆☆☆
-   Disks are mounted to Sandboxed-Container after you set specific container environment variables. Performance: ☆☆☆☆☆ |
|NAS|-   virtio-fs \(default\). Performance: ☆☆☆☆
-   Disks are mounted to Sandboxed-Container after you set specific container environment variables. Performance: ☆☆☆☆☆ |
|OSS|virtio-fs. Performance: ☆☆☆☆|
|Network plug-in|-   The Terway network plug-in is used. Its network performance is 20% to 30% higher than Flannel. Terway supports features such as NetworkPolicy. This allows you to define the networking policies and enable bandwidth throttling for pods. For more information, see [Terway plug-in](/intl.en-US/User Guide for Kubernetes Clusters/Network/Overview.md).
-   Flannel, which supports virtual private cloud \(VPC\) routes.

|Flannel|
|Monitoring and alerting|-   Enhanced monitoring of disks and network conditions for pods that use Sandboxed-Container.
-   Integrated with Cloud Monitor. This facilitates cluster monitoring and alerting.

|Monitoring of disks and network conditions is unavailable for pods that use Sandboxed-Container.|
|Stability|☆☆☆☆☆|☆☆|

## Applicable scenarios of Sandboxed-Container

This section describes the applicable scenarios of Sandboxed-Container.

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1107846161/p144019.png)

-   Scenario 1: Sandboxed-Container can run untrusted code and applications in isolated containers. This is not supported by containers in runC.
    -   Security risks of runC

        ![Risks](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1107846161/p144020.png)

        -   runC isolates containers by using namespaces and control groups \(cgroups\). This exposes containers to security risks.
        -   All containers on a node share the host kernel. If the kernel has a vulnerability, malicious code may escape to the host and then infiltrate the backend network. Malicious code execution may cause privilege escalation, compromise sensitive data, and destroy system services and other applications.
        -   Attackers may also exploit application vulnerabilities to infiltrate the internal network.
        You can implement the following measures to reduce the security risks of containers in runC.

        -   Seccomp: filters system calls.
        -   SElinux: limits the permissions of container processes, files, and users.
        -   Capability: limits the capability of container processes.
        -   dockerd rootless mode: forbids users from using root permissions to run the Docker daemon and containers.
        The preceding measures can enhance the security of containers in runC and reduce attacks on the host kernel by using malicious containers. However, container escapes and host kernel vulnerabilities remain unresolved.

    -   Sandboxed-Container prevents potential risks based on container isolation

        ![Security](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1107846161/p144021.png)

        In a Sandboxed-Container runtime environment, applications that have potential security risks are deployed on sandboxed and lightweight virtual machines. Each of the virtual machines has a dedicated guest OS kernel. If a security vulnerability is detected on a guest OS kernel, the attack is limited to one sandbox and does not affect the host kernel or other containers. The Terway network plug-in allows you to define networking policies for pods. This enables system isolation, data isolation, and network isolation for sandboxed containers.

-   Scenario 2: Sandboxed-Container resolves common issues of runC containers, such as fault spreading, resource contention, and performance interference.

    ![Fault isolation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6665359951/p95596.png)

    Kubernetes provides easy deployment of different containers on a single node. However, cgroups are not optimized to address resource contention. Resource-intensive applications such as CPU-intensive and I/O-intensive applications may compete for the same resources. This causes significant fluctuations in response time and increases the overall response time. Exceptions or faults on an application may spread to the hosting node and disrupt the running of the cluster. For example, memory leaks and frequent core dumps of an application may overload the node, and exceptions on a container may trigger a host kernel bug that results in an entire system failure. Through dedicated guest OS kernels and hypervisors, Sandboxed-Container addresses the issues that are common with runC containers. The issues include failure spreading, resource contention, and performance interference.

-   Scenario 3: Sandboxed-Container supports multi-tenant services.

    An enterprise may need to isolate applications of multiple business lines or departments. For example, a finance department requires high security applications. However, other non-security-sensitive applications do not have high security requirements. Containers in runC fail to eliminate the potential risks brought by untrusted applications. In this scenario, you can implement the following countermeasures:

    -   Deploy multiple independent single-tenant clusters. For example, deploy financial business and other non-security-sensitive business in different clusters.
    -   Deploy a multi-tenant cluster and separate applications of different business lines by using namespaces. The resource of a node is exclusive to a single business line. This solution provides data isolation for coordination with the resource quotas and network policies to implement multi-tenant isolation. Compared with multiple single-tenant clusters, this solution focuses on fewer management planes and thus reduces management costs. However, this solution cannot avoid resource waste on nodes caused by low resource utilization of some tenants.

        ![Tenants](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1107846161/p144022.png)

    Sandboxed-Container allows you to isolate untrusted applications by using sandboxed virtual machines. This prevents the risks of container escapes. This also allows you to deploy different containerized applications on each node, which brings the following benefits:

    -   Resource scheduling is simplified.
    -   A node is no longer exclusive to a service. This improves node resource usage and reduces resource fragments and cluster resource costs.
    -   Sandboxed containers use lightweight virtual machines to provide almost the same performance as containers in runC.
    ![Nodes](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1107846161/p144023.png)


