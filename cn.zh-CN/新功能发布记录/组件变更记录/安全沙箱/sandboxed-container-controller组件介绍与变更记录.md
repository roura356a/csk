---
keyword: [sandboxed-container-controller, 安全沙箱]
---

# sandboxed-container-controller组件介绍与变更记录

本文为您介绍sandboxed-container-controller组件的功能并展示该组件的变更记录。

## 组件介绍

sandboxed-container-controller是安全沙箱运行时提供的专用控制器组件，帮助在安全沙箱中实现NAS/云盘直挂，从而使得安全沙箱场景下存储IO性能达到与宿主机直挂相当的效果。

## 变更记录

**2020年8月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.1-8484958-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.0.1-8484958-aliyun|2020年8月26日|支持ACK沙箱容器2.0，新增安全沙箱容器专用的PodQuota准入控制器，可根据Pod内所有容器CPU和内存资源总和设置Pod沙箱规格。|此次升级不会对业务造成影响。|

**2020年6月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.0-e408663-aliyun|registry.cn-beijing.aliyuncs.com/acs/sandboxed-container-controller:v1.0.0-e408663-aliyun|2020年6月10日|InitContainer的NAS公有镜像地址修改为私有镜像地址。|此次升级不会对业务造成影响。|

**2020年3月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.0-a8b276f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.0.0-a8b276f-aliyun|2020年3月26日|支持NAS、云盘的沙箱直通功能，直通后的存储性能与宿主机挂载模式性能一致，避免9PFS带来的严重性能损耗。|此次升级不会对业务造成影响。|

