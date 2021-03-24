---
keyword: [aesm, 变更版本]
---

# aesm

本文介绍aesm组件的功能并展示该组件变更记录。

## 组件介绍

Intel® SGX Architectural Enclave Service Manager （Intel® SGX AESM）是Intel® SGX的系统组件，主要提供了SGX1 Enclave启动支持，EPID配置和证明，以及一些平台相关的服务。ACK TEE提供的aesm组件是以DaemonSet形式部署Intel® SGX AESM服务的组件。

## 变更记录

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|2.7.1-4a8c95b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aesm:2.7.1-4a8c95b-aliyun|2020年2月21日|新增Intel® SGX AESM组件，主要提供SGX1 Enclave启动支持，EPID配置和证明，以及一些平台相关的服务。|此次升级不会对业务造成影响。|

