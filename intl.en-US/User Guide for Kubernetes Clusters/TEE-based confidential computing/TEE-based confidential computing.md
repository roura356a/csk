---
keyword: [TEE-based confidential computing, managed Kubernetes cluster for confidential computing, sandboxed container]
---

# TEE-based confidential computing

Container Service for Kubernetes \(ACK\) clusters support confidential computing based on trusted execution environments \(TEEs\). This topic describes the concept, core features, scenarios, and solutions of TEE-based confidential computing. This topic also describes the collaboration between TEE-based confidential computing and sandboxed containers.

## Concept

ACK provides TEE-based confidential computing which is a cloud-native and all-in-one solution based on hardware encryption technologies. TEE-based confidential computing ensures data security, integrity, and confidentiality. It simplifies the development and delivery of trusted or confidential applications at lower costs. Confidential computing allows you to isolate sensitive data and code in a TEE. This prevents the data and code from being accessed by the rest of the system. The data stored within TEEs is inaccessible to external applications, the BIOS, the operating system, the kernel, administrators, O&M engineers, cloud service providers, and hardware components except the CPU. This reduces the possibility of data leakage and simplifies data management.

![CONCEPT](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8626069161/p102354.png)

## Core features

-   Ensures the integrity of code and data in the cloud.
-   Encrypts data and code to prevent data breaches.
-   Enables lifecycle management of data.

## Scenarios

-   **Blockchains**

    Enhances confidentiality and security for transaction processing, consensus, smart contracts, and key storage.

-   **Key management**

    Deploys the key management feature in an enclave. This feature is similar to a hardware security module \(HSM\).

-   **Genetic computing**

    Ensures data confidentiality by isolating sensitive data in computing scenarios where multiple parties are involved.

-   **Finance**

    Supports secure payments and transactions.

-   **AI**

    Protects intellectual property rights by encrypting confidential information such as models.

-   **Edge computing**

    Supports secure and confidential communications among clouds, edges, and terminals.

-   **Data sharing and computing**

    Protects data from breaches when users or vendors share data for higher economic value.


## Solution

The following figure shows the TEE-based confidential computing V1.1.

![Solution](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8626069161/p102355.png)

ACK released managed Kubernetes clusters for confidential computing based on Intel Software Guard Extensions \(SGX\) 2.0. This simplifies the management of trusted or confidential applications and saves costs on the delivery of these applications. Confidential computing ensures the integrity and confidentiality of data and code in public clouds and prevents access from cloud vendors. For more information about how to create a managed Kubernetes cluster for confidential computing, see [Create a managed Kubernetes cluster for confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Create a managed Kubernetes cluster for confidential computing.md).

Make sure that the following requirements are met:

-   When you create worker nodes for the cluster, select Elastic Compute Service \(ECS\) instances of the **c7t security-enhanced compute optimized** **instance family**.

    **Note:**

    -   The **security-enhanced compute optimized** instance family is in private preview. This instance family provides a limited stock of ECS instances and is not covered by terms of service level agreement \(SLA\). If you want to use ECS instances of this instance family, apply for ECS instances based on your minimum requirement.
    -   Intel Ice Lake supports the remote attestation service only based on Intel Software Guard Extensions Data Center Attestation Primitives \(SGX DCAP\). Remote attestation services based on Intel Enhanced Privacy Identification \(EPID\) are not supported. You must adapt your applications before you can use the remote attestation service. For more information about the remote attestation service, see [attestation-services](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/attestation-services.html).
-   The SGX 2.0 driver and TEE SDK are automatically installed when the nodes of the cluster are initialized. TEE SDK is a development kit provided by Alibaba Cloud to develop applications for confidential computing. This kit provides development models and programming interfaces that are consistent with those of Intel Linux SGX SDK.
-   By default, Intel SGX Architectural Enclave Service Manager \(AESM\) DaemonSet is installed. This allows applications of SGX 2.0 to access AESM.
-   You can use the SGX device plug-in developed by Alibaba Cloud to detect, manage, and schedule memory resources in Enclave Page Cache \(EPC\) of SGX nodes.

## TEE-based confidential computing collaborates with sandboxed containers

**Containers in runC are vulnerable to attacks**

A container in runC shares a kernel with the host. When a container escape vulnerability occurs in the kernel, malicious applications in the container may escape into the backend system. This may harm other applications and the entire system.

![OS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3765359951/p102356.png)

**Sandboxed containers isolate malicious applications and block attacks**

Sandboxed containers provide enhanced isolation based on the lightweight Kangaroo framework. Each pod runs on an independent operating system and kernel. When a vulnerability occurs in a kernel, only the pod that runs on this kernel is affected. This protects other applications and the backend system.

![Block attacks](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7658767061/p102471.png)

**TEE-based confidential computing protects applications in use**

TEE-based confidential computing is an encrypted computing solution provided by ACK. It protects sensitive code and data, such as IP addresses, keys, and confidential communications.

Cloud computing is a technology that brings benefits to enterprises. However, when you migrate data to the cloud, the possibility of data breaches becomes a core concern. Data breaches may occur in the following scenarios:

-   Attacks
-   Untrusted cloud vendors
-   Security flaws of cloud infrastructure
-   Unqualified O&M personnel and administrators

![Applications](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3765359951/p102358.png)

**TEE-based confidential computing collaborates with sandboxed containers to isolate malicious applications and protect sensitive data**

TEE-based confidential computing and sandboxed containers provide different features. You can combine the features in your cluster to isolate malicious applications and protect sensitive applications and data.

![Collaboration of TEE-based confidential computing and sandboxed containers](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8658767061/p102470.png)

**Related topics**  


[Create a managed Kubernetes cluster for confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Create a managed Kubernetes cluster for confidential computing.md)

[t1830927.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Create a security sandbox cluster/Create a managed Kubernetes cluster that runs sandboxed containers.md)

