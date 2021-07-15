---
keyword: [AESM, Intel SGX AESM]
---

# Intel SGX AESM

This topic describes Intel \(R\) Software Guard Extensions \(SGX\) Architectural Enclave Service Manager \(AESM\) and lists the latest changes to Intel \(R\) SGX AESM.

## Introduction

Intel \(R\) SGX AESM is a system component of Intel SGX. This component provides launch support for SGX Enclave, key provisioning, and remote attestation services. In trusted execution environments \(TEEs\) provided by Container Service for Kubernetes \(ACK\), Intel \(R\) SGX AESM runs on DaemonSets in ACK clusters.

## Usage notes

By default, Intel \(R\) SGX AESM is installed in ACK clusters. You can use Intel \(R\) SGX AESM without extra configurations.

## Release notes

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|2.13.100.4-bionic1-d83e54d-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aesm:2.13.100.4-bionic1-d83e54d-aliyun|2021-04-30|The Intel \(R\) SGX AESM component is added. The component provides launch support for SGX Enclave, key provisioning, and remote attestation services.|No impact on workloads|

