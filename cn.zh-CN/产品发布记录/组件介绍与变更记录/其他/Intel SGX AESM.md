---
keyword: [AESM, Intel SGX AESM]
---

# Intel SGX AESM

本文介绍AESM组件的功能并展示该组件变更记录。

## 组件介绍

Intel® SGX Architectural Enclave Service Manager （Intel® SGX AESM）是Intel® SGX的系统组件，主要提供了SGX Enclave启动支持，密钥配置、远程认证等服务。ACK TEE提供的AESM组件是以DaemonSet形式部署Intel® SGX AESM服务的组件。

## 使用说明

Intel SGX AESM组件默认安装，无需配置即可使用。

## 变更记录

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|2.13.100.4-bionic1-d83e54d-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aesm:2.13.100.4-bionic1-d83e54d-aliyun|2021年04月30日|新增Intel® SGX AESM组件，主要提供SGX Enclave启动支持，密钥配置、远程认证等服务。|此次升级不会对业务造成影响。|

