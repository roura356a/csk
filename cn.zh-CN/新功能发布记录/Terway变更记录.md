# Terway变更记录 {#concept_wk1_grd_qfb .concept}

本文为您介绍了Terway相关内容的最新动态。

## 2019年4月 {#section_8rm_l63_kn8 .section}

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.9.15-g3957085-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.15-g3957085-aliyun|2019年4月11日|修复Terway组件升级过程中偶发性失败的问题。|此次升级不会对业务造成影响。|

## 2019年3月 {#section_vtx_d7f_inn .section}

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.9.14-ga0346bb-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.14-ga0346bb-aliyun|2019年3月28日| -   修复Terway在meta server被流控时获取弹性网卡信息失败的问题。
-   修复创建容器时上报**failed to move veth to host netns: file exists** 的问题。
-   新增对弹性网卡状态定期扫描，对于异常释放的弹性网卡会定期回收的功能。
-   优化健康检查：Terway健康检查方式从HTTP路径检查优化成TCP端口检查。

 |此次升级不会对业务造成影响。|

