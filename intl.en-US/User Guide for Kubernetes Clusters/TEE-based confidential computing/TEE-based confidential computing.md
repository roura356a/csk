---
keyword: [TEE-based confidential computing, managed confidential computing cluster, sandboxed container]
---

# TEE-based confidential computing

Container Service for Kubernetes clusters support confidential computing based on a trusted execution environment \(TEE\). This topic describes the purpose, features, scenarios, and solutions of TEE-based confidential computing. It also describes the collaboration between TEE-based confidential computing and sandboxed containers.

## Concept

Container Service for Kubernetes provides TEE-based confidential computing. This is a cloud-native and all-in-one solution based on hardware encryption technologies. TEE-based confidential computing ensures the data security, integrity, and confidentiality. It also simplifies the development, delivery, and management costs of trusted or confidential applications. Confidential computing allows you to isolate sensitive data and code in a TEE. This prevents the rest part of the system from accessing the data and code. Encrypted data in the TEE is unavailable to other applications, the BIOS, operating systems, kernels, administrators, O&M engineers, cloud vendors, and hardware components except CPUs. This reduces the possibility of data breaches and simplifies data management.

![CONCEPT](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3765359951/p102354.png)

## Features

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

    Protects intellectual property rights by encrypting confidential information such as data models.

-   **Edge computing**

    Supports secure and confidential communications among clouds, edges, and terminals.

-   **Data sharing and computing**

    Protects data from breaches when users or vendors share data for higher economic value.


## Solution

The following figure shows the TEE-based confidential computing v1.0.

![Solution](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3765359951/p102355.png)

Container Service for Kubernetes supports Intel Software Guard Extensions \(SGX\)-based confidential computing in a managed cluster. This feature simplifies management and delivery of trusted or confidential applications at reduced costs. Confidential computing ensures the integrity and confidentiality of data and code in public clouds and prevents access from cloud vendors. For more information about how to create a managed Kubernetes cluster for confidential computing, see [Create a managed Kubernetes cluster that supports confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Create a managed Kubernetes cluster that supports confidential computing.md).

Make sure that the following requirements are met:

-   Worker nodes must be ECS Bare Metal instances of the **ecs.ebmhfg5.2xlarge** type. This instance type supports Intel SGX.
-   The SGX driver and SGX Platform Software \(PSW\) are automatically installed during node initialization.
-   By default, Intel SGX Architectural Enclave Service Manager \(AESM\) DaemonSet is installed. This allows SGX applications to access AESM.
-   The SGX device plug-in that is developed by Alibaba Cloud simplifies the detection, management, and scheduling of memory resources in Enclave Page Cache \(EPC\) of SGX nodes.

## TEE-based confidential computing collaborates with sandboxed containers

**Containers in runC are vulnerable to attacks**

A container in runC shares a kernel with the host. When a container escape vulnerability is detected in the kernel, malicious applications in the container may escape into the backend system. This may cause negative effects to other applications and the entire system.

![OS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3765359951/p102356.png)

**Sandboxed containers isolate malicious applications and block attacks**

Sandboxed containers provide enhanced isolation based on the lightweight Kangaroo framework. Each pod runs on an independent operating system and kernel. When a vulnerability is detected in a kernel, only the pod that runs on this kernel is affected. This protects other applications and the backend system.

![Block attacks](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7658767061/p102471.png)

**TEE-based confidential computing protects applications in use**

TEE-based confidential computing is an encrypted computing solution provided by Container Service for Kubernetes. It protects sensitive code and data, such as IP addresses, keys, and confidential communications.

Cloud computing is a technology that brings benefits to enterprises. However, when you migrate data to the cloud, the possibility of data breaches becomes a core concern. Data breaches may occur in the following scenarios:

-   Attacks
-   Untrusted cloud vendors
-   Security flaws of cloud infrastructures
-   Unqualified O&M personnel and administrators

![Application](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3765359951/p102358.png)

**TEE-based confidential computing collaborates with sandboxed containers to isolate malicious applications and protect sensitive data**

TEE-based confidential computing and sandboxed containers provide different features. You can combine the features in the cluster to isolate malicious applications and protect sensitive applications and data.

![Collaboration of TEE-based confidential computing and sandboxed containers](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8658767061/p102470.png)

**Related topics**  


[Create a managed Kubernetes cluster that supports confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Create a managed Kubernetes cluster that supports confidential computing.md)

[t1830927.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Create a managed Kubernetes cluster that runs sandboxed containers.md)

