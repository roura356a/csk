---
keyword: [node-problem-detector, ack-node-problem-detector, 事件监控]
---

# ack-node-problem-detector

ack-node-problem-detector是ACK基于社区开源项目进行改造和增强的集群节点异常事件监控组件，以及对接第三方监控平台功能的组件。您可以根据需求使用该组件加入定制化的节点监控插件，扩大节点问题监控范围。

有关社区开源项目node-problem-detector的详细信息，请参见[node-problem-detector](https://github.com/kubernetes/node-problem-detector)。

有关ack-node-problem-detector的安装，使用场景以及新增插件的功能，请参见[事件监控](/cn.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/事件监控.md)。

## 变更记录

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v0.6.3-28-160499f|registry.aliyuncs.com/acs/node-problem-detector:v0.6.3-28-160499f|2020年7月27日|-   优化OOM Killing事件消息，加入Pod的名字、命名空间、UID等信息。
-   优化check\_fd插件的执行效率。
-   优化节点PID水位的事件通知。
-   升级网络问题检测插件。
-   新增监控节点系统盘inode水位报警插件。 |

