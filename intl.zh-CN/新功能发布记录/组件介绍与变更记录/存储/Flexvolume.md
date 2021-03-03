---
keyword: [Flexvolume, Flexvolume组件变更记录]
---

# Flexvolume

Flexvolume支持数据卷的挂载、卸载功能。ACK默认提供云盘、NAS、OSS三种存储卷的挂载能力。本文介绍Flexvolume组件相关内容的最新动态。

## 2020年07月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.14.8.109|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.8.109-649dc5a-aliyun|2020年07月24日|支持使用快照创建的云盘作为PV数据卷。|
|v1.14.8.104|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.8.104-24bde93-aliyun|2020年07月09日|-   基础镜像Alpine版本升级到3.11.6。
-   支持HongKong，ShenZhen-Finance内网域名。
-   极速NAS创建子目录判断优化。 |

## 2020年04月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.14.8.88|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.8.88-00a6585-aliyun|2020年04月14日|-   修复Size相同时云盘扩容问题。
-   修复云盘挂载时`/dev/vd* not exist`类问题解决。
-   托管集群权限收敛。 |

## 2020年03月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.14.8.66|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.8.66-1d0843f-aliyun|2020年03月06日|-   CPFS驱动部署方式更新。
-   OSS支持挂载子目录。
-   支持内网API域名。 |

## 2019年10月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.14.6.15|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.6.15-8d3b7e7-aliyun|2019年10月21日|-   支持云盘的自动扩容。
-   支持云盘数据卷的监控功能。
-   更新CPFS驱动。 |

## 2019年08月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.12.6.52|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.12.6.52-f6604e5-aliyun|2019年08月20日|支持NAS Mount Config设置，默认配置最优化内核参数。|

## 2019年04月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.12.6.16|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.12.6.16-1f4c6cb-aliyun|2019年04月16日|-   NAS添加Sysctl配置。
-   磁盘添加Waitforattach。 |
|v1.12.6.11|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.12.6.11-ab46951-aliyun|2019年04月09日|-   CPFS驱动升级。
-   云盘Detach加锁，避免并行挂载、卸载盘符问题。
-   更新NAS卸载时`-f`的使用限制。
-   NAS默认支持V3版本。
-   OSS支持secret方式输入AK。 |

## 2018年12月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.11.2.32|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.11.2.32-af2d48c-aliyun|2018年12月10日|-   修复OSS随着Kubelet重启问题。
-   增加云盘挂载等待时间。 |

## 2018年10月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.11.2.5|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.11.2.5-85c062f-aliyun|2018年10月18日|修复云盘并发挂载问题。|
|v1.11.2|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.11.2-9139592-aliyun|2018年10月17日|-   修复高并发NAS多重挂载问题。
-   修复云盘挂载慢，Device为空问题。 |

## 2018年09月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.10.4|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.10.4-bdab325-aliyun|2018年09月27日|修复云盘卸载失败问题。|

## 2018年08月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.10.4|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.10.4-dfe877b-aliyun|2018年08月22日|支持NFS Options配置。|

## 2018年05月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.9.7|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.9.7-42e8198-aliyun|2018年05月31日|修复Kubelet重启云盘重新挂载问题。|

## 2018年02月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.8.4|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.8.4-bcfda92-aliyun|2018年02月05日|-   默认支持OSS、NAS、Disk三种存储卷。
-   鉴权方式：OSS使用输入AK的方式，云盘使用RamRole授权方式。
-   创建集群时，自动部署Flexvolume插件。 |

