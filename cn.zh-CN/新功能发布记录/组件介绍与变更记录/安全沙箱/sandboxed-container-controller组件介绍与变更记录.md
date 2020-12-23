---
keyword: [sandboxed-container-controller, 安全沙箱]
---

# sandboxed-container-controller组件介绍与变更记录

本文为您介绍sandboxed-container-controller组件的功能并展示该组件的变更记录。

## 组件介绍

sandboxed-container-controller是安全沙箱运行时提供的专用控制器组件，旨在增强和扩展安全沙箱的基本功能。支持以下功能：

-   支持安全沙箱Pod自定义内核参数配置。
-   支持安全沙箱VM规格自动计算和配置。
-   支持云盘/NAS直通沙箱。

## 变更记录

**2020年12月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.1.1-55d545f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.1.1-55d545f-aliyun|2020年12月22日|强制覆盖Pod中手动设置的两个Annotations：securecontainer.alibabacloud.com/cpus和securecontainer.alibabacloud.com/memory。|此次升级不会对业务造成影响。|

**2020年11月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.1.0-3b3d499-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.1.0-3b3d499-aliyun|2020年11月26日|新增对runV Pod沙箱内核参数的自定义配置。|此次升级不会对业务造成影响。|
|v1.0.3-e993d8f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.0.2-8ac82bf-aliyun|2020年11月12日|因Docker不支持RuntimeClass特性，新增准入控制器PodEraseRuntimeclassRunc，当`pod.spec.runtimeClassName`设置为`runc`时，此配置项会被重置为空值。|此次升级不会对业务造成影响。|

**2020年08月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.1-8484958-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.0.1-8484958-aliyun|2020年08月26日|支持ACK沙箱容器2.0，新增安全沙箱容器专用的PodQuota准入控制器，可根据Pod内所有容器CPU和内存资源总和设置Pod沙箱规格。|此次升级不会对业务造成影响。|

**2020年06月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.0-e408663-aliyun|registry.cn-beijing.aliyuncs.com/acs/sandboxed-container-controller:v1.0.0-e408663-aliyun|2020年06月10日|InitContainer的NAS公有镜像地址修改为私有镜像地址。|此次升级不会对业务造成影响。|

**2020年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.0-a8b276f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.0.0-a8b276f-aliyun|2020年03月26日|支持NAS、云盘的沙箱直通功能，直通后的存储性能与宿主机挂载模式性能一致，避免9PFS带来的严重性能损耗。|此次升级不会对业务造成影响。|

