---
keyword: [静态数据卷, 动态数据卷]
---

# 存储Flexvolume概述

容器服务ACK支持自动绑定阿里云云盘、阿里云文件存储NAS（Network Attached Storage ）、阿里云对象存储OSS（Object Storage Service）存储服务。本文介绍支持的存储服务和数据卷的情况。

容器服务支持静态存储卷和动态存储卷，每种数据卷的支持情况如下。

|阿里云存储|静态数据卷|动态数据卷|
|-----|-----|-----|
|阿里云云盘|云盘静态存储卷支持PV/PVC使用方式|支持|
|阿里云NAS|NAS静态存储卷支持以下两种使用方式：-   通过Flexvolume插件通过PV/PVC使用
-   通过Kubernetes的NFS驱动使用

|支持|
|阿里云OSS|OSS静态存储卷支持PV/PVC使用方式|不支持|

