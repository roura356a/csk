---
keyword: [sgx-device-plugin, release notes]
---

# sgx-device-plugin

This topic describes sgx-device-plugin and lists the latest changes to the component.

## Overview

sgx-device-plugin is a plug-in that applies to Container Service for Kubernetes \(ACK\). The plug-in is developed by Alibaba Cloud and Ant Financial. sgx-device-plugin facilitates the use of Intel \(R\) Software Guard Extensions \(SGX\) in containers. Intel \(R\) SGX is a set of CPU instructions provided by Intel. Intel \(R\) SGX increases the security of application code and data. This extra protection allows you to prevent against disclosure and malicious tampering. For more information, see [software-guard-extensions](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions.html).

**Features**

sgx-device-plugin provides the following features:

-   Intel \(R\) SGX is supported. You do not need to enable the privilege mode.
-   The Enclave Page Cache \(EPC\) size can be automatically retrieved.
-   Declarative EPC resource allocation is supported.

**Prerequisites**

sgx-device-plugin depends on the following components and tools:

-   [Intel SGX Drivers](https://github.com/intel/linux-sgx-driver)
-   [Intel \(R\) SGX Platform Software \(PSW\)](https://github.com/intel/linux-sgx). This component is required if you want to enable Intel \(R\) SGX Architectural Enclave Service Manager \(Intel \(R\) SGX AESM\).
-   The Kubernetes version must be V1.10 and later.
-   The Go version must be V1.10 and later.

**FAQ**

-   Can I deploy sgx-device-plugin in Kubernetes clusters that are deployed outside Alibaba Cloud?

    Yes, sgx-device-plugin can be deployed in all types of Kubernetes clusters. However, you can run sgx-device-plugin on only SGX-enabled nodes.

-   Can I use sgx-device-plugin to control the EPC size for SGX-enabled containers?

    No, you cannot use sgx-device-plugin to control the EPC size for SGX-enabled containers. The EPC size limit specified by the alibabacloud.com/sgx\_epc\_MiB parameter applies to only kube-scheduler. Intel \(R\) SGX Driver does not support this parameter.

-   Is sgx-device-plugin open source?

    Yes, sgx-device-plugin is open source. For more information, see [sgx-device-plugin](https://github.com/AliyunContainerService/sgx-device-plugin).


## Release notes

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0-5f5b5ef-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sgx-device-plugin:v1.0.0-5f5b5ef-aliyun|February 21, 2020|-   Intel \(R\) SGX is supported. You do not need to enable the privilege mode.
-   The Enclave Page Cache \(EPC\) size can be automatically retrieved.
-   Declarative EPC resource allocation is supported.

|No impact on workloads.|

