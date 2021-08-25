---
keyword: [logtail-windows, 容器日志收集插件]
---

# logtail-windows

本文介绍logtail-windows组件的信息、使用说明和变更记录。

## 组件介绍

logtail-windows是ACK集群上使用的容器日志收集插件，用于在阿里云上配合SLS服务对Windows容器进行日志收集。

## 使用说明

logtail-windows目前仍然需要手动安装。具体操作，请参见[为Windows节点安装Logtail v1.0.x](/cn.zh-CN/Kubernetes集群用户指南/Windows容器/使用Logtail收集Windows容器日志.md)。

logtail-windows v1.0.x适用于所有Windows工作节点。在v1.0.20及之后版本，不再支持Windows Server Core，version 1903的工作节点，您需要完成业务容器的SAC（Semi-Annual Channel）版本适配，使用Windows Server Core，version 1909或version 2004工作节点。

logtail-windows v1.1.x依赖Windows工作节点上的特权托管进程，镜像大小相较于v1.0.x的镜像要缩小90%，启动速度更快。logtail-windows v1.1.x仅适用于从集群节点池创建节点时已含有`windows.alibabacloud.com/deployment-topology=2.0`标签的Windows工作节点。如果您创建完Windows工作节点后，再在Windows工作节点上添加`windows.alibabacloud.com/deployment-topology=2.0`标签，则该Windows工作节点不支持logtail-windows v1.1.x。

## 变更记录

**2021年08月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.20|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.0.20|2021年08月21日|-   更新basic镜像，同时覆盖原版本号。
-   支持 Multiple Windows Server Release 1809（10.0.17763.2114）、1909（10.0.18363.1556）、2004（10.0.19041.1165）。

|此次升级不会对业务造成影响。|
|v1.1.18-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.1.18-aliyun.1|2021年08月20日|-   更新basic镜像，同时覆盖原版本号。
-   支持 Multiple Windows Server Release 1809（10.0.17763.2114）、1909（10.0.18363.1556）、2004（10.0.19041.1165）。

|此次升级不会对业务造成影响。|

**2021年07月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.1.18-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.1.18-aliyun.1|2021年07月05日|-   更新basic镜像，同时覆盖原版本号。
-   减少容器镜像的大小，由2.7 GB下降到220 MB。
-   支持Multiple Windows Server Release 1809（10.0.17763.1999）、1909（10.0.18363.1556）、2004（10.0.19041.1052）。

|此次升级不会对业务造成影响。|

**2021年06月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.1.18-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.1.18-aliyun.1|2021年06月17日|-   引入rancher/wins服务作为特权代理。
-   减少容器镜像的大小，由2.7 GB下降到220 MB。
-   支持Multiple Windows Server Release 1809（10.0.17763.1935）、1909（10.0.18363.1556）、2004（10.0.19041.985）。

|此次升级仅适用于安装了rancher/wins服务的Windows工作节点。|

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.20|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.0.20|2021年04月09日|支持Multiple Windows Server Release 1809（10.0.17763.1817）、1909（10.0.18363.1440） 、2004（10.0.19041.867），不再支持Windows Server Release 1903。

|-   此次升级不适用Windows Server Core，version 1903工作节点。
-   此次升级不对Windows Server Core，version 1809、version 1909和version 2004工作节点的业务造成影响。
-   之后迭代的版本不再支持Windows Server Core，version 1903工作节点。 |

**2021年01月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.19|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.0.19|2021年01月27日|-   支持在Internet下创建log store。
-   支持Multiple Windows Server Release 1809（10.0.17763.1697）、1903（10.0.18362.1256）、1909（10.0.18363.1316）、2004（10.0.19041.746）。

|此次升级不会对业务造成影响。|
|v1.0.18|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.0.18|2021年01月25日|-   修复对深圳金融云的支持。
-   升级logtail到v1.0.0.18。
-   支持Multiple Windows Server Release 1809（10.0.17763.1697）、1903（10.0.18362.1256）、1909（10.0.18363.1316）、2004（10.0.19041.746）。

|此次升级不会对业务造成影响。|
|v1.0.10|registry-vpc.$\{region\}.aliyuncs.com/acs/logtail-windows:v1.0.10|2021年01月10日|-   支持logtail v1.0.0.10。
-   支持Multiple Windows Server Release 1809（10.0.17763.1637）、1903（10.0.18362.1256）、1909（10.0.18363.1256）、2004（10.0.19041.685）。

|此次升级不会对业务造成影响。|

