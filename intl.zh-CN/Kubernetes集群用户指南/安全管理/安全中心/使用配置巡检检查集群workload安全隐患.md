---
keyword: [配置巡检, workload配置, 安全隐患]
---

# 使用配置巡检检查集群workload安全隐患

本文将介绍如何使用配置巡检功能来扫描集群中workload配置的安全隐患和介绍巡检报告相关信息，帮助您实时了解当前状态下运行应用的配置是否有安全隐患。

您已经成功创建一个Kubernetes 1.14.8或以上版本的托管版或专有版集群，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。

## 扫描集群workload

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏选择**安全管理** \> **配置巡检**。

5.  如果您未安装巡检组件的话，在**安装巡检组件**下方单击**开始安装**，然后单击**开始使用配置巡检**。如果您已经安装请忽略此步骤。

6.  在配置巡检页面右上方，单击**立即执行巡检**。

7.  等待巡检扫描完成后，单击**刷新**查看相应的巡检报告。

8.  在配置巡检页面右上方，单击**定期巡检**开启或禁用定期自动执行配置巡检的功能。


## 巡检报告

巡检报告中主要展示最近执行的巡检扫描结果，主要包括以下几类信息：

-   扫描结果概览，包括总的检查条目数、检查各个资源项数目及百分比、整体的健康度。
-   各大类扫描结果统计，包括报告健康检查、镜像、网络、资源、安全等大类的结果。
-   各个workload配置的详细扫描结果，包括资源类别、资源名称、命名空间、检查类型、检查项、检查结果等内容。

配置巡检功能会扫描并展示以下检查项的扫描结果。

|检查项名称|检查的内容及安全风险|修复建议|
|-----|----------|----|
|hostNetworkSet|通过检查workload的Pod Spec中是否配置了hostNetwork：true，检查是否配置了共享使用主机的网络namespace。如果配置了，存在Pod中容器攻击主机网络、嗅探主机网络数据的风险。|修改Pod Spec，删除hostNetwork字段。

示例：

![1](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9195659951/p129766.png) |
|hostIPCSet|通过检查workload的Pod Spec中是否配置了hostIPC：true，检查是否配置了共享使用主机的IPC namespace。如果配置了，存在Pod中容器攻击主机上进程、嗅探主机上进程数据的风险。|修改Pod Spec，删除hostIPC字段。

示例：

![2](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9195659951/p129768.png) |
|hostPIDSet|通过检查workload的Pod Spec中是否配置了hostPID：true，检查是否配置了共享使用主机的PID namespace。如果配置了，存在Pod中容器攻击主机上进程、采集主机上进程数据的风险。|修改Pod Spec，删除hostPID字段。 示例：

![3](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9195659951/p129769.png) |
|hostPortSet|通过检查workload的Pod Spec中是否配置了hostPort，检查是否配置了把容器中监听的端口映射到主机指定端口上。如果配置了，存在挤占主机可用端口以及被非预期的请求方请求容器端口的风险。|修改Pod Spec，删除hostPort字段。 示例：

![4](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9195659951/p129770.png) |
|runAsRootAllowed|通过检查workload的Pod Spec中是否未配置runAsNonRoot：true，检查是否配置了允许使用root用户运行容器。如果配置了，存在被容器中的恶意进程入侵用户应用、入侵主机甚至入侵整个集群的风险。|修改Pod Spec，增加runAsNonRoot: true。 示例：

![5](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0295659951/p129771.png) |
|runAsPrivileged|通过检查workload的Pod Spec中是否配置了privileged：true，检查是否配置了允许以特权模式运行容器。如果配置了，存在被容器中的恶意进程入侵用户应用、入侵主机甚至入侵集群的风险。|修改Pod Spec，删除privileged字段。 示例：

![6](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0295659951/p129772.png) |
|privilegeEscalationAllowed|通过检查workload的Pod Spec中是否配置了allowPrivilegeEscalation：true，检查是否配置了允许容器中的子进程拥有比父进程更高的权限。如果配置了，存在被容器中的恶意进程实现越权操作的风险。|修改Pod Spec，删除allowPrivilegeEscalation字段。 示例：

![7](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0295659951/p129773.png) |
|capabilitiesAdded|通过检查workload的Pod Spec中capabilities字段，检查是否配置了允许容器中的进程拥有SYS\_ADMIN、NET\_ADMIN、ALL等特权Linux Capabilities。如果配置了，存在被容器中的恶意进程通过这些特权入侵用户应用、入侵或破坏组件/集群的风险。|修改Pod Spec，根据实际需求只添加必需的Linux Capabilities，删除不需要的Linux Capabilities。 不依赖额外Linux Capabilities，删除所有不需要的Linux Capabilities。示例：

![8](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0295659951/p129774.png)

只添加必需的Linux Capabilities，删除所有不需要的Linux Capabilities。示例：

![9](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0295659951/p129775.png) |
|notReadOnlyRootFileSystem|通过检查workload的Pod Spec中是否未配置readOnlyRootFilesystem：true，检查是否未配置容器中的文件系统是不可修改的。如果未配置的话，存在被容器中的恶意进程恶意修改系统文件的风险。|修改Pod Spec，增加readOnlyRootFilesystem: true，如果有需要修改某个目录下文件的需求，可以通过volumeMounts实现。 示例：

![10](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0295659951/p129776.png)

如果有需要修改某个目录下文件的需求，通过volumeMounts实现修改文件的需求。

示例：

![11](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0295659951/p129777.png) |
|cpuRequestsMissing|通过检查workload的Pod Spec中是否未配置resources.requests.cpu字段，检查是否未配置运行容器所需的最少CPU资源。如果未配置的话，Pod有被调度到资源紧张的节点上的风险，可能会出现容器内进程运行缓慢的风险。|修改Pod Spec，增加resources.requests.cpu字段。 示例：

![12](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0295659951/p129778.png) |
|cpuLimitsMissing|通过检查workload的Pod Spec中是否未配置resources.limits.cpu字段，检查是否未配置运行容器所需的最大CPU资源。如果未配置的话，存在被容器内的异常进程消耗大量节点资源，甚至把整个节点或集群的资源销耗殆尽的风险。|修改Pod Spec，增加resources.limits.cpu字段。 示例：

![13](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0295659951/p129779.png) |
|memoryRequestsMissing|通过检查workload的Pod Spec中是否未配置resources.requests.memory字段，检查是否未配置运行容器所需的最少内存资源。如果未配置，Pod有被调度到资源紧张的节点上的风险，可能会出现容器内进程OOM的风险。|修改Pod Spec，增加resources.requests.memory字段。 示例：

![14](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0295659951/p129780.png) |
|memoryLimitsMissing|通过检查workload的Pod Spec中是否未配置resources.limits.memory字段，检查是否未配置运行容器所需的最大内存资源。如果未配置的话，存在被容器内的异常进程消耗大量节点资源，甚至把整个节点或集群的资源销耗殆尽的风险。|修改Pod Spec，增加resources.limits.memory字段。 示例：

![15](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0295659951/p129781.png) |
|readinessProbeMissing|通过检查workload的Pod Spec中是否未配置readinessProbe字段，检查是否未配置检测容器内应用能否正常处理请求的探针。如果未配置的话，存在在容器内应用异常无法处理请求时仍旧有请求发过来导致业务异常的风险。|修改Pod Spec，增加readinessProbe字段。 示例：

![16](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0295659951/p129783.png) |
|livenessProbeMissing|通过检查workload的Pod Spec中是否未配置livenessProbe，检查是否未配置检测容器内应用是否出现异常需要重启容器的探针。如果未配置，存在在容器内应用异常需要重启容器才能恢复时未及时重启导致业务异常的风险。|修改Pod Spec，增加livenessProbe字段。 示例：

![17](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/1295659951/p129784.png) |
|tagNotSpecified|通过检查workload的Pod Spec中的image字段的值是否未包含镜像tag或者使用了latest作为镜像tag，检查是否未配置运行容器时使用指定tag的容器镜像。如果未配置，存在在运行容器时运行了非预期的容器镜像版本导致业务异常的风险。|修改Pod Spec，修改image字段，使用指定的镜像tag并且不要使用latest作为镜像tag。 示例：

![18](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/1295659951/p129785.png) |

-   [Configure a Security Context for a Pod or Container](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)
-   [Configure Liveness, Readiness and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

  


