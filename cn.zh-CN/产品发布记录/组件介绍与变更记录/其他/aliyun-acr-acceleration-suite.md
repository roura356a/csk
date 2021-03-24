---
keyword: [aliyun-acr-acceleration-suite, aliyun-acr-acceleration-suite组件变更记录]
---

# aliyun-acr-acceleration-suite

本文介绍aliyun-acr-acceleration-suite组件的功能并展示该组件变更记录。

## 组件介绍

aliyun-acr-acceleration-suite是提供镜像按需加载加速能力的客户端插件，以DaemonSet形式部署在Worker节点上。通过配合使用ACR的加速镜像自动转换功能，您可以在业务部署中使用加速镜像，实现镜像数据免全量下载和在线解压，大幅提升应用分发效率，享受极致的弹性体验，更多信息，请参见[按需加载容器镜像]()。

**说明：** 仅支持在版本≥1.16.9的托管版和专有版集群上使用加速镜像。且创建集群时需要设置容器运行时为Docker运行时，操作系统为Aliyun linux 2.1903、CentOs7.6、CentOs7.7、CentOs7.8、CentOs7.9。

## 变更记录

**2021年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|0.2.0|registry-vpc.cn-hangzhou.aliyuncs.com/acr-toolkit/aliyun-acr-acceleration-suite:v0.2.0.0-b745758c-aliyun|2021年3月15日|-   mutating webhook增加就绪和存活HTTP探针。
-   支持workload维度打加速标签以启用按需加载能力。标签key为`k8s.aliyun.com/image-accelerate-mode`，标签value为`on-demand`。

**说明：** 关于设置按需加载的具体操作，请参见[按需加载容器镜像]()。


|此次升级不会对业务造成影响。|

**2020年11月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|0.1.0|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-acceleration-suite:v0.1.0.0-00e2f5e1-aliyun|2020年11月19日|主要提供相关镜像存储插件管理、镜像仓库服务的访问配置，以及Pod加速镜像自动注入等功能。|建议在业务低峰期进行安装和升级。|

