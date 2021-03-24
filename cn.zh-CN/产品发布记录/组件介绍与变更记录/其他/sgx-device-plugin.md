---
keyword: [sgx-device-plugin, 变更版本]
---

# sgx-device-plugin

本文为您介绍sgx-device-plugin组件的功能并展示该组件变更记录。

## 组件介绍

sgx-device-plugin由阿里云容器服务团队和蚂蚁金服安全计算团队针对Intel SGX联合开发的Kubernetes Device Plugin，可以让您更容易的在容器中使用SGX。Intel\(R\) Software Guard Extensions（Intel\(R\) SGX）是Intel为软件开发者提供的安全技术，用于防止指定的代码和数据的窃取和恶意篡改。详细介绍请参见[software-guard-extensions](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions.html)。

**主要功能**

sgx-device-plugin主要提供以下功能：

-   无需开启容器特权模式即可使用SGX。
-   支持自动获取EPC内存大小。
-   支持容器声明式EPC内存分配。

**依赖组件**

sgx-device-plugin主要依赖以下组件：

-   [Intel SGX Drivers](https://github.com/intel/linux-sgx-driver)
-   [Intel SGX PSW\(Platform Software\)](https://github.com/intel/linux-sgx)（如果您需要AESM服务）
-   Kubernetes版本≥1.10
-   Go版本≥1.10

**FAQ**

-   可以把sgx-device-plugin组件部署到私有Kubernetes集群吗？

    可以，您可以把sgx-device-plugin组件部署在任何Kubernetes集群上，但sgx-device-plugin组件只能运行在SGX的节点上。

-   sgx-device-plugin组件是否可以帮助应用限制EPC大小？

    不可以，alibabacloud.com/sgx\_epc\_MiB指定的EPC大小限制仅用于Kubernetes集群的调度，SGX驱动目前还不支持EPC大小限制。

-   sgx-device-plugin组件是否开源？

    sgx-device-plugin组件是开源的，详细介绍请参见[sgx-device-plugin](https://github.com/AliyunContainerService/sgx-device-plugin)。


## 变更记录

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.0-5f5b5ef-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sgx-device-plugin:v1.0.0-5f5b5ef-aliyun|2020年2月21日|-   无需开启容器特权模式即可使用SGX。
-   支持自动获取EPC内存大小。
-   支持容器声明式EPC内存分配。

|此次升级不会对业务造成影响。|

