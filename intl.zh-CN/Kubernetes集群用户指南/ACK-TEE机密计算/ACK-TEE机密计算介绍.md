---
keyword: [ACK-TEE机密计算, 加密计算托管集群, ACK安全沙箱]
---

# ACK-TEE机密计算介绍

本文介绍ACK-TEE（ACK-Trusted Execution Environment）机密计算的概念、核心功能、适用场景、产品方案，以及ACK-TEE和ACK安全沙箱的配合作用等。

## 基础概念

为有强安全诉求的用户提供了基于硬件加密技术的云原生一站式机密计算容器平台 ，它可以帮助您保护数据使用（计算）过程中的安全性、完整性和机密性，同时简化了可信或机密应用的开发、交付和管理成本。机密计算可以让您把重要的数据和代码放在一个特殊的可信执行加密环境（Trusted Execution Environment，TEE）中，而不会暴露给系统其他部分。其他应用、BIOS、OS、Kernel、管理员、运维人员、云厂商、甚至除了CPU以外的其他硬件均无法访问机密计算平台数据，极大减少敏感数据的泄露风险，为您提供了更好的控制、透明度和隐秘性。

![CONCEPT](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6680959161/p102354.png)

## 核心功能

-   保护并验证云端代码和数据的完整性。
-   加密数据和代码，避免在使用过程中被恶意窥探和窃取。
-   保障您对数据全生命周期的控制。

## 适用场景

-   **区块链**

    增强事务处理、共识、智能合约以及密钥存储的隐私性和安全性。

-   **密钥管理**

    把密钥管理功能放在Enclave中，提供类似硬件安全模块（HSM）的功能。

-   **基因计算**

    敏感数据，对敏感数据的多方计算提供了隐私隔离。

-   **金融**

    保护支付、交易的安全性。

-   **AI**

    保护模型等核心机密数据，保护知识产权。

-   **边缘计算**

    保护云、边、端三位一体相互通信的安全以及隐秘性。

-   **数据共享与计算**

    不同用户或厂商之间相互共享数据以便挖掘更大的数据经济价值，但不想把自己的数据泄露给对方。


## 产品方案

ACK-TEE1.1版的产品方案图如下所示。

![产品方案](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3373959161/p102355.png)

ACK已经上线了基于Intel SGX2.0技术的加密计算托管集群，以便帮助您简化可信或机密应用的管理和交付成本。在公有云处理数据和代码时，保护您数据和代码的完整性和隐秘性，从可信计算基础中摆脱云厂商的影响。有关如何在ACK上直接创建加密计算托管集群，请参见[创建加密计算托管集群](/intl.zh-CN/Kubernetes集群用户指南/ACK-TEE机密计算/创建加密计算托管集群.md)。

创建加密计算托管集群说明如下：

-   Worker节点需选择**规格族**为**安全增强计算型c7t**的型号。

    **说明：**

    -   由于**安全增强计算型**实例规格族当前是邀测阶段，资源有限，无法承诺SLA。如您确认继续申请，请按照业务最小需求申请。
    -   Intel IceLake仅支持基于Intel SGX DCAP的远程证明方式，不支持基于Intel EPID方式的远程证明方式，您的程序可能需要适配后才能正常使用远程证明功能。关于远程证明的更多信息，请参见[attestation-services](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/attestation-services.html)。
-   节点初始化时会自动安装SGX2.0驱动以及TEE-SDK。TEE SDK是阿里云提供的开发加密计算应用的开发者套件，该套件提供与Intel Linux SGX SDK一致的开发模型和编程接口。
-   为了便于SGX2.0应用访问AESM，集群默认安装了AESM DaemonSet。
-   通过自研的sgx-device-plugin，您可轻松实现对SGX节点EPC内存资源的发现、管理以及调度。

## ACK-TEE机密计算和ACK安全沙箱配合工作的应用场景

**传统OS（操作系统）容器攻击模型**

传统OS容器（或称为RunC容器）和宿主机共享Kernel，当内核出现漏洞，容器中恶意应用（如三方或您的应用）会利用漏洞逃逸并渗透到后端系统，危害其他应用和整个服务系统。

![OS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4606659951/p102356.png)

**ACK安全沙箱隔离恶意应用并阻断攻击**

ACK安全沙箱容器是基于轻量虚拟机技术（Kangaroo）实现的强隔离。每个Pod都有自己独立的OS和Kernel，这样恶意应用的攻击面从宿主机级别缩小至Pod级别，从而保护其他应用和后端系统。

![阻断攻击](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4606659951/p102471.png)

**ACK-TEE加密保护运行中的应用**

ACK-TEE是ACK基于Kubernetes提供的机密计算解决方案，用于保护您的敏感代码和数据，如IP保护、密钥保护、隐秘通信等。

云计算为您和企业提供了极大的便利，然而数据泄露成了上云过程中最大的担忧之一。数据泄露可能来源：

-   黑客攻击
-   云厂商的不可信任
-   云基础设施安全缺陷
-   运维和管理人员

![应用](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5606659951/p102358.png)

**安全沙箱和ACK-TEE配合隔离恶意应用并保护敏感应用**

ACK安全沙箱和ACK-TEE定位完全不同，它们之间可以相互配合，这样在您的集群中，既可以隔离恶意应用，又可以保护敏感的应用和数据，做到真正的双向保护。

![双剑合一](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5606659951/p102470.png)

**相关文档**  


[创建加密计算托管集群](/intl.zh-CN/Kubernetes集群用户指南/ACK-TEE机密计算/创建加密计算托管集群.md)

[t1830927.md\#](/intl.zh-CN/Kubernetes集群用户指南/安全沙箱/创建安全沙箱集群/创建安全沙箱托管版集群.md)

