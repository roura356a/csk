# 修改Swarm SLB配置 {#concept_371767 .concept}

在SLB中，监听到请求之后，需要将请求转发到后面服务器；而后端服务器可以通过3种方式挂载到监听规则中，分别为**默认服务器组**、**主备服务器组**、**虚拟服务器组**。

其中，默认服务器组和主备服务器组要求组内机器的后端机器端口是一样的，无法满足我们Kubernetes 引流的诉求（Swarm机器默认是通过9080端口从SLB引流，而Kubernetes 集群在我们前一步配置NodePort服务时，只能通过30000~32767之间端口做引流）。所以，我们只能通过虚拟服务器组的方式配置后端服务器，进而实现给Kubernetes 集群机器引流。您可以按照以下步骤操作。

## 确认后端服务器组配置 {#section_dqd_x70_bl7 .section}

确认当前Swarm SLB是否是已通过**虚拟服务器组**方式挂载后端机器。

1.  登录[负载均衡管理控制台](https://slbnext.console.aliyun.com/)，在左侧导航栏选择**实例** \> **实例管理**。
2.  进入实例管理页面，在目标实例右侧查看后端服务器的类型。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301866/155954567748304_zh-CN.png)

    -   如果后端服务器类型为默认服务器，请继续下一步骤。
    -   如果后端服务器类型为虚拟服务器组。则直接进入[调整虚拟服务器组](#section_c0j_jmi_y3t)步骤。

## 创建虚拟服务器组 {#section_l4d_368_8tj .section}

1.  在实例管理页面，单击目标实例，在实例详情页面，单击**虚拟服务器组**页签。
2.  单击**创建虚拟服务器组**，从右侧滑出编辑虚拟服务器组页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301866/155954567748305_zh-CN.png)

3.  填写对应的内容并单击**确定**，创建一个虚拟服务器组，请参考[管理虚拟服务器组](../../../../intl.zh-CN/用户指南/后端服务器/管理虚拟服务器组.md#)。

    **说明：** 

    -   添加云服务时，swarm集群机器需全部选择，避免万一切换到该虚拟服务器组时，后端服务器撑不住已有流量，而Kubernetes 集群机器可以任选1或多台。
    -   swam监听端口和权重设置一定要跟原先**默认服务器组**或**主备服务器组**配置保持一致。

## 修改监听规则，切换到新服务器组 {#section_iop_iev_83f .section}

在创建好虚拟服务器组后，您需要修改SLB监听规则，将服务器组从**默认服务器组**或**主备服务器组**，切换到上述创建的虚拟服务器组上。

1.  在实例管理页面，单击目标实例，进入实例详情页面，在**监听**页签目标端口右侧单击**配置**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301866/155954567748306_zh-CN.png)

2.  在配置监听页面，修改配置并单击**下一步**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301866/155954567748307_zh-CN.png)

3.  在后端服务器中，单击**虚拟服务器组**，选择服务器群组为swarm&K8s，确认公网/内网IP地址及端口（需要重点确认虚拟服务器组中的swarm机器列表及端口是否正确）正确后，单击**下一步**保存更新。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301866/155954567848309_zh-CN.png)

4.  返回**监听**页签，可以看到目标端口的服务器组已切换为虚拟swarm&k8s。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301866/155954567848310_zh-CN.png)


## 确认业务请求是否正常 {#section_ut2_5za_cqn .section}

**请重点监控一段时间**，确认线上流量业务请求处理是否正常，可通过**云监控** \> **主机监控**，**云监控** \> **日志监控**或者客户自身搭建的监控体系等手动观察业务服务响应情况。

## 调整虚拟服务器组 {#section_c0j_jmi_y3t .section}

在确认将SLB 的后端服务器切换成虚拟服务器组类型没问题之后，下一步我们要做的事情就是调整虚拟服务器组，加入Kubernetes 机器，从而实现将生产流量部分引流到Kubernetes 集群。

1.  在实例管理页面，单击目标实例，进入实例详情页面，在**监听**页签目标端口右侧单击服务器组。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301866/155954567848311_zh-CN.png)

2.  在**虚拟服务器组**页签，目标服务器组右侧单击**编辑**，在右侧滑出栏虚拟服务器编辑页面中，可通过添加按钮，选择添加Kubernetes 服务器并保存，即完成Kubernetes 机器的挂载引流。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301866/155954567848312_zh-CN.png)

    **说明：** 

    -   Kubernetes 集群机器可以按业务灰度流量需要选择加入一台/多台，方便做流量控制。
    -   Kubernetes 机器端口设置，需要跟前面NodePort设置的端口号一样。

        区间应该为30000~32767，这里示例值为30080端口，映射到Kubernetes 中的gateway-swarm-slb服务。

    -   如有需要，可以通过权重更精细地控制Kubernetes 集群的流量占比。

## 确认流量进去Kubernetes 集群 {#section_1ta_wvo_s8e .section}

**请重点监控一段时间**，确认线上流量业务请求处理是否正常；并确认Kubernetes 集群是否按虚拟服务器组设置的机器比例及权重配置引流成功。

**说明：** 这里只是通过 **Swarm SLB 引入部分生产流量来验证** Kubernetes 集群业务功能是否正常；对应的 Swarm SLB **此时并不会下线**，需要等到后续客户端切流完成之后，在[下线Swarm集群](intl.zh-CN/最佳实践/Swarm迁移Kubernetes/下线Swarm集群.md#)章节下线 Swarm 集群及 Swarm SLB 。

