---
keyword: [ack-arena组件介绍, ack-arena组件使用说明, ack-arena组件变更记录]
---

# ack-arena

ack-arena组件对开源Arena的安装做进一步简化，能够实现在控制台一键安装Arena的目标。本文介绍ack-arena组件的信息、使用说明和变更记录。

## 组件介绍

Arena是基于Kubernetes的机器学习轻量级解决方案，支持数据准备、模型开发，模型训练、模型预测的完整生命周期，提升数据科学家工作效率。同时和阿里云的基础云服务深度集成，支持GPU共享、CPFS等服务，可以运行阿里云优化的深度学习框架，最大化使用阿里云异构设备的性能和成本的效益。

ack-arena组件用于简化在ACK集群上安装Arena操作，您可以在容器服务控制台的组件管理或应用目录页面一键安装ack-arena，实现在ACK集群快速部署Arena的目标。

## 使用说明

关于ack-arena组件的使用，请参见[安装Arena](/intl.zh-CN/云原生AI用户指南/环境准备/安装Arena.md)。

## 变更记录

**2021年06月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|0.8.5-264b96a-aliyun|-   registry.cn-beijing.aliyuncs.com/acs/arena-binary-installer:0.8.5-264b96a-aliyun
-   registry.cn-beijing.aliyuncs.com/acs/arena-deploy-manager:0.8.5-264b96a-aliyun

|2021年06月08日|-   修复arena top node中GPU总显存数为0的问题。
-   修复cron job的RBAC缺失问题。
-   支持nvidia triton类型的推理任务。
-   支持执行arena-uninstall卸载arena。

|此次升级不会对业务造成影响。|

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|0.8.0-ba37c8a-aliyun|-   registry.cn-beijing.aliyuncs.com/acs/arena-binary-installer:0.8.0-ba37c8a-aliyun
-   registry.cn-beijing.aliyuncs.com/acs/arena-deploy-manager:0.8.0-ba37c8a-aliyun

|2021年04月06日|-   修复spark job无法提交的问题。
-   修复没有chief Pod情况下获取logviewer的问题。
-   支持Arena Python SDK和Java SDK。
-   支持提交seldon类型推理任务。
-   支持产生Arena多租户使用的kubeconfig脚本。
-   支持指定tfjob训练任务的角色启动顺序。

|此次升级不会对业务造成影响。|

**2021年01月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|0.7.1-3559f56-aliyun|-   registry.cn-beijing.aliyuncs.com/acs/arena-binary-installer:0.7.1-3559f56-aliyun
-   registry.cn-beijing.aliyuncs.com/acs/arena-deploy-manager:0.7.1-3559f56-aliyun

|2021年01月27日|修复et-operator未安装在arena-system下的问题。|此次升级不会对业务造成影响。|
|0.7.0-c6f5800-aliyun|-   registry.cn-beijing.aliyuncs.com/acs/arena-binary-installer:0.7.0-c6f5800-aliyun
-   registry.cn-beijing.aliyuncs.com/acs/arena-deploy-manager:0.7.0-c6f5800-aliyun

|2021年01月25日|-   修复arena serve delete删除多任务问题。
-   更改arena list、arena get、arena serve list、arena serve get的输出格式。
-   支持Arena Go SDK。
-   支持阿里云ARMS Prometheus。
-   arena get支持使用`-g`选项显示GPU。
-   arena logs支持`-c`选项指定容器。

|此次升级不会对业务造成影响。|

