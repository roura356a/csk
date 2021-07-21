---
keyword: [runtime security, trusted software supply chain, infrastructure security]
---

# Security system overview

This topic describes the security system of Container Service for Kubernetes \(ACK\) from the following three perspectives: runtime security, trusted software supply chains, and infrastructure security. The security system is supported by a variety of features provided by ACK, including security inspection, policy management, runtime monitoring and alerting, image scans, image signing, cloud-native application delivery chain, default security, identity management, and fine-grained access control.

![61](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5168389061/p203109.png)

## Runtime security

-   Security inspection

    Developers must follow the principle of least privilege when they configure pod templates. Otherwise, attackers can exploit unnecessary pod permissions that are granted to users to launch escape attacks against containers. ACK supports security inspections for runtimes. You can use this feature to inspect pod configurations of running applications and check for potential risks in real time.

    An inspection report is generated after a security inspection is performed. The report includes the description of each inspection item and suggestions on how to fix the related security issues. You can also configure periodic inspections. The results of each periodic inspection are logged to the specified Logstore in Log Service. For more information, see [Use the inspection feature to detect security risks in the workloads of an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use the inspection feature to detect security risks in the workloads of an ACK cluster.md).

-   Policy management

    Pod security policy is an important approach to verify the security of pod configurations before pods are deployed. This ensures that applications are running in secure pods. You can use pod security policies to enforce security verification on pods when applications are deployed. Pod security policies function as a scan engine, such as AppArmor and SELinux, to provide in-depth protection for clusters.

    A pod security policy is a cluster-level Kubernetes-native resource that is enforced by using an [admission controller](https://kubernetes.io/blog/2019/03/21/a-guide-to-kubernetes-admission-controllers/) of the Kubernetes API server. The admission controller enforces security verification of pod configurations when applications are deployed. If the pod configuration fails to meet the conditions that are defined in a specified pod security policy, the Kubernetes API server rejects the request to create the pod.

    By default, pod security policy control is enabled in ACK clusters. You can enable or disable the admission controller of pod security policies in the ACK console. You can also customize pod security policies and bind a pod security policy to a specified service account in the ACK console. This makes pod security policies much easier to use and avoids issues where existing applications cannot be redeployed after the applications are bound to pod security policies. For more information, see [Configure and enforce pod security policies](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Configure and enforce pod security policies.md).

-   Runtime monitoring and alerting

    Cloud-native applications are deployed in containers after they pass the authentication and admission control of the API server. However, in accordance with the zero trust principle for application security, monitoring and alerting are required to ensure the security of application runtimes. Therefore, ACK is deeply integrated with the alerting and vulnerability detection capabilities of Security Center. This allows cluster administrators to monitor application runtimes and raise alerts upon security events. Runtime monitoring and alerting are used to prevent the following attacks that are launched against containers:

    -   Loading of malicious images
    -   Implanting of viruses and malicious programs
    -   Intrusion into containers
    -   Container escapes and high-risk operations
    On the details page of an ACK cluster, choose **Security** \> **Runtime Security** to view the received alerts in real time. You can also follow the instructions on the page to check and handle alerts. For more information, see [Use Runtime Security to monitor ACK clusters and configure alerts](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use Runtime Security to monitor ACK clusters and configure alerts.md).

-   Sandboxed-Container

    Sandboxed-Container is an alternative to the Docker runtime. Sandboxed-Container allows you to run applications in a sandboxed and lightweight virtual machine that has a dedicated kernel. This enhances resource isolation and improves security.

    Sandboxed-Container is suitable in scenarios such as untrusted application isolation, fault isolation, performance isolation, and load isolation among multiple users. Sandboxed-Container provides enhanced security, has minor impacts on application performance, and offers the same user experience as Docker in terms of logging, monitoring, and elastic scaling. For more information about Sandboxed-Container, see [Sandboxed-Container overview](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Sandboxed-Container overview.md).

-   TEE-based confidential computing

    ACK provides trusted execution environment \(TEE\)-based confidential computing. This is a cloud-native and all-in-one solution that is built on hardware encryption technologies. TEE-based confidential computing ensures the security, integrity, and confidentiality of data. It also simplifies the development and release of trusted and confidential applications and reduces management costs. Therefore, TEE-based confidential computing is suitable for use in the finance industry and public service sectors that require high security.

    TEE-based confidential computing allows you to store sensitive data and code in a TEE. This prevents the other parts of the system from accessing the data and code. Data and code in a TEE are inaccessible to other applications, the BIOS, the operating system, the kernel, administrators, O&M personnel, cloud service providers, or hardware components except the CPU. This simplifies data management and reduces the risk of sensitive data leakage. For more information, see [TEE-based confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/TEE-based confidential computing.md).


## Trusted software supply chains

-   Image scans

    Container Registry allows you to scan all Linux-based container images for known vulnerabilities. After you run a scan, you will receive a report that contains information about the detected vulnerabilities and suggestions on how to fix them. Image scans help you reduce the risks in container images. Container Registry is also integrated with the scan engine of Security Center. This engine can be used to detect system vulnerabilities, application vulnerabilities, and malicious samples in images. For more information about image scans, see [Scan container images]().

-   Image signing

    When you manage container images, you can use the content trust mechanism to verify that the images and their publishers are trusted. Image publishers can use digital signatures to sign images. The digital signatures are stored in Container Registry. Then, you can verify the signatures of images to ensure that only images signed by trusted authorities are deployed. This reduces the risk of malicious code execution and ensures the security and traceability of container images from the software supply chain to application deployment. For more information about how to sign an image and verify the signature, see [Use kritis-validation-hook to automatically verify the signatures of container images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Image/Use kritis-validation-hook to automatically verify the signatures of container images.md).

-   Cloud-native application delivery chain

    Container Registry provides a cloud-native application delivery chain for you to develop containerized applications with high security and efficiency. This chain covers image builds, image scans, image synchronization on a global scale, and image deployment. You can also customize fine-grained security policies. The cloud-native application delivery chain makes the entire lifecycle of application development secure, observable, and traceable. After you use the cloud-native application delivery chain, you need only to submit the code once for each application. The image is distributed and deployed across all regions worldwide in a secure and efficient manner. This upgrades the development pipeline from DevOps to DevSecOps. For more information about the cloud-native application delivery chain, see [Create a delivery chain]().


## Infrastructure security

-   Default security

    In an ACK cluster, the security of nodes and components on the control plane is reinforced based on [Center for Internet Security \(CIS\) Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes/). In addition, the configurations of all system components are reinforced by following the ACK best practices for security. No component image contains critical vulnerabilities that are identified by Common Vulnerabilities and Exposures \(CVE\).

    -   Each newly created ACK cluster is assigned a security group that allows only inbound Internet Control Message Protocol \(ICMP\) packets from the Internet. By default, you cannot connect to an ACK cluster by using SSH over the Internet. If you want to connect to an ACK cluster by using SSH over the Internet, see [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use SSH to connect to an ACK cluster.md).
    -   You can enable Internet access for nodes in an ACK cluster by using NAT gateways. This secures Internet access and reduces security risks.
    -   Worker nodes in a managed Kubernetes cluster are assigned Resource Access Management \(RAM\) roles that are authorized by following the principle of least privilege. These RAM roles have only the minimum permissions on Alibaba Cloud resources. For more information, see [\[Product Changes\] ACK reduces the permissions of worker RAM roles in managed Kubernetes clusters](t1885958.md#).
-   Identity management

    The communication and data transmission between all components in an ACK cluster must be secured by using TLS-based authentication. In addition, ACK automatically renews the certificates of system components. The kubeconfig file contains credentials that are required when you connect to the API server of a cluster. You can log on to the ACK console or call the ACK API as a RAM user or by assuming a RAM role and then obtain the kubeconfig file. For more information, see [DescribeClusterUserKubeconfig](/intl.en-US/API Reference/Clusters/DescribeClusterUserKubeconfig.md). The cluster credentials are maintained by ACK. If the cluster credentials in the returned kubeconfig file are leaked, you must immediately revoke the kubeconfig file. For more information, see [Revoke a KubeConfig credential](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Revoke a KubeConfig credential.md).

    When you create an ACK cluster, you can enable **service account token volume projection**. This feature enhances the security when you use service accounts in applications. For more information, see [Enable service account token volume projection](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Infrastructure security/Enable service account token volume projection.md).

-   Fine-grained access control

    ACK provides fine-grained access control of Kubernetes resources in an ACK cluster based on role-based access control \(RBAC\). This is a basic but essential reinforcement for application security. On the **Authorizations** page in the ACK console, you can assign RBAC roles to grant fine-grained permissions that are scoped to namespaces. This authorization method provides the following benefits:

    -   ACK provides the following predefined RBAC roles: administrator, O&M engineer, developer, and restricted user. This provides an easy way to grant permissions to employees in hierarchical departments of an enterprise.
    -   ACK allows you to grant permissions on multiple clusters or authorize multiple RAM users at a time.
    -   ACK allows you to authorize a RAM user to be assumed by a RAM role.
    -   ACK allows you to assign custom cluster roles.
    For more information, see [Assign an RBAC role to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).

    You can install the gatekeeper component on the Add-ons page in the ACK console. This component provides fine-grained access control by using the Open Policy Agent \(OPA\) policy engine. For more information, see [gatekeeper](/intl.en-US/Release notes/System Component change Records/Security/gatekeeper.md).

-   Auditing

    ACK is deeply integrated with Log Service. ACK allows you to collect, retrieve, and visualize the following types of audit log:

    -   The audit log of the API server of a cluster. This type of audit log records the operations that are performed by users when they access the cluster. You can check the audit log to trace each operation if required. This is a key component to maintain clusters in a secured way. On the **Cluster Auditing** page, you can view a variety of auditing reports and also configure alerts for operations that are performed on specified resource types based on the log content. For more information, see [Enable cluster auditing](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Infrastructure security/Enable cluster auditing.md).
    -   The audit log of Ingress traffic. Multiple visualized traffic reports are provided to show the status of Ingresses in a cluster. The reports provide information such as the page views \(PVs\) and unique visitors \(UVs\) of services, the ratios of request successes and failures, and the latency. Exceptions can also be automatically detected by using the machine learning algorithms provided by Log Service and the time series analysis algorithms. For more information, see [Monitor nginx-ingress and analyze the access log of nginx-ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Monitor nginx-ingress and analyze the access log of nginx-ingress.md).
    -   The audit log of event monitoring. Event monitoring records cluster events in the audit log. You can diagnose exceptions and security risks in a cluster based on these events. For more information, see [Event monitoring](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Event monitoring.md).
-   Secret encryption

    Kubernetes Secrets are encoded in Base64 when they are stored in etcd. To further reinforce the security of the stored Kubernetes Secrets, you can use keys that are created in Key Management Service \(KMS\) to encrypt Secrets of professional managed Kubernetes clusters. For more information, see [Use KMS to encrypt Kubernetes Secrets](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Use KMS to encrypt Kubernetes Secrets.md).


