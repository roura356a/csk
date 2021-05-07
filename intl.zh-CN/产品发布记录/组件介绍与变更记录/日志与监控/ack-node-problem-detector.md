---
keyword: [node-problem-detector, ack-node-problem-detector, 事件监控]
---

# ack-node-problem-detector

ack-node-problem-detector是ACK基于社区开源项目进行改造和增强的集群节点异常事件监控组件，以及对接第三方监控平台功能的组件。承载ACK集群的节点异常发现以及事件中心的功能。您可以根据需求使用该组件加入定制化的节点监控插件，扩大节点问题监控范围。

## 组件介绍

-   ack-node-problem-detector的DaemonSet负责巡检节点异常。关于社区开源项目node-problem-detector的更多信息，请参见[node-problem-detector](https://github.com/kubernetes/node-problem-detector)。
-   当开启事件中心功能时，若开启任意一项sink参数，ack-node-problem-detector组件将会包含ack-node-problem-detector-eventer等组件，负责监听集群内所有事件并上报到事件中心。关于kube-eventer的更多信息，请参见[kube-eventer](https://github.com/AliyunContainerService/kube-eventer)。

## 使用说明

关于ack-node-problem-detector的安装，使用场景以及新增插件的功能，请参见[事件监控](/intl.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/事件监控.md)。

## 变更记录

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.2.5|-   ack-node-problem-detector: registry.aliyuncs.com/acs/node-problem-detector:v0.6.3-28-160499f
-   kube-eventer: registry-vpc.\_\_ACK\_REGION\_ID\_\_.aliyuncs.com/acs/kube-eventer-amd64:v1.2.4-0f5aaee-aliyun
-   kube-event-init: registry.\{ .Values.controller.regionId \}.aliyuncs.com/acs/kube-eventer-init:1.5-5e0e7c1-aliyun

|2021年04月25日|-   修复开启事件中心时kube-system命名空间下的kube-event-init会出现414 Request Too Large的问题。
-   优化eventer list-watch机制，以避免ETCD请求流量过大。更多信息，请参见[eventer list-watch](https://github.com/AliyunContainerService/kube-eventer/pull/188)。
-   优化kube-eventer对部分系统事件的时间戳解析错误问题。更多信息，请参见[fix FailedScheduling event write to sls with wrong timestamp](https://github.com/AliyunContainerService/kube-eventer/pull/168)。 |

**2020年07月**

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v0.6.3-28-160499f|registry.aliyuncs.com/acs/node-problem-detector:v0.6.3-28-160499f|2020年07月27日|-   优化OOM Killing事件消息，加入Pod的名字、命名空间、UID等信息。
-   优化check\_fd插件的执行效率。
-   优化节点PID水位的事件通知。
-   升级网络问题检测插件。
-   新增监控节点系统盘inode水位报警插件。 |

