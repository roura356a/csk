---
keyword: 安全组常见问题
---

# 容器安全FAQ

本文主要为您介绍安全组常见问题。

-   [为什么容器之间网络不通？](#section_7in_bdt_tfu)
-   [如何给Kubernetes集群指定安全组？](#section_y2v_i2p_x4f)
-   [集群审计功能是否可以取消或者在创建集群后再部署？](#section_jnt_2kt_s4u)
-   [Kubernetes专有版本集群怎么更换证书有效期，以及更换各个组件的证书？](#section_7qf_ryg_o3w)
-   [Pod无法创建，报错详情：no providers available to validate pod request](#section_lil_6sd_usm)
-   [Secret在新建的Namespace下面无法使用](#section_js7_ku8_n29)
-   [无法挂载default-token](#section_5ic_ehe_2om)
-   [审计日志查询方法](#section_zhr_qfz_ofq)

## 为什么容器之间网络不通？

下面为您介绍容器服务Kubernetes集群由于安全组导致网络不通的问题原因及解决方法。

-   入方向**授权对象**为**Pod 网络CIDR**，且**协议类型**为**全部**的规则被删除。
    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在控制台左侧导航栏中，单击**集群**。
    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
    4.  选择**集群资源**页签，单击**虚拟专有网络 VPC**右侧的链接。

        ![虚拟专有网络](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2712207161/p148432.png)

    5.  在资源管理页签，单击**安全组**下的数字。
    6.  单击目标安全组**操作列**的**配置规则**。
    7.  在入方向页面，单击**手动添加**。
    8.  填写**协议类型**、**端口范围**、**授权对象**，并单击**保存**。

        **说明：**

        -   **协议类型**请选择**全部**。
        -   **授权对象**填写为Pod的网段地址。

            Pod的网段地址（Pod网络CIDR）可在容器服务管理控制台集群详细信息页面，集群信息区域查看。

            ![集群信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5195659951/p37009.png)

            **授权对象**的设置，可参见[安全组应用案例ECS安全组配置操作指南](/intl.zh-CN/安全/安全组/安全组应用案例.md)。

        入方向**授权对象**为**Pod网络CIDR**，且**协议类型**为**全部**的规则已添加。

        ![入方向](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5195659951/p148458.png)

-   新增ECS实例的安全组与集群所在的安全组不同。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在控制台左侧导航栏中，单击**集群**。
    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
    4.  选择**集群资源**页签，单击**虚拟专有网络 VPC**。

        ![虚拟专有网络](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2712207161/p148432.png)

    5.  在专有网络详情页面，单击资源管理页签**安全组**下的数字，跳转到云服务器ECS管理控制台的安全组列表页面查看安全组的详细信息。
    6.  在安全组列表页面，查看安全组的名称。

        ![安全组列表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5195659951/p37012.png)

    7.  在云服务器ECS管理控制台，选择左侧导航栏**实例与镜像** \> **实例**。
    8.  在实例列表页面，单击目标实例的**操作**列的**更多** \> **网络和安全组** \> **加入安全组**，进入ECS实例加入安全组页面。

        ![实例列表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5195659951/p37135.png)

    9.  单击**安全组**复选框右侧的下拉箭头，输入步骤6查询到的集群安全组名称。

        ![ECS实例加入安全组](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5195659951/p37136.png)

    10. 单击**确定**。
    执行结果：

    1.  在云服务器管理控制台左侧导航栏，选择**实例与镜像** \> **实例**，在实例列表页面，单击目标实例的名称。
    2.  在安全组列表区域，可看到ECS实例已加入集群所在的安全组。

        ![安全组列表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5195659951/p37607.png)


## 如何给Kubernetes集群指定安全组？

当前暂不支持给Kubernetes集群指定安全组。但是创建Kubernetes集群时，容器服务ACK会自动创建一个默认安全组，您可以通过修改默认安全组的规则，达到指定安全组的效果。

## 集群审计功能是否可以取消或者在创建集群后再部署？

可以。具体操作，请参见[使用集群审计功能](/intl.zh-CN/Kubernetes集群用户指南/安全/容器应用安全/使用集群审计功能.md)。

## Kubernetes专有版本集群怎么更换证书有效期，以及更换各个组件的证书？

-   集群临近过期前两个月左右您会收到站内和短信通知，收到通知后在控制台集群列表页面单击证书更新按钮即可。具体操作，请参见[更新即将过期证书](/intl.zh-CN/Kubernetes集群用户指南/安全/基础设施安全/更新即将过期证书.md)。
-   如果Kubernetes集群证书已过期，具体操作，请参见[更新Kubernetes集群已过期的证书](/intl.zh-CN/Kubernetes集群用户指南/集群/更新Kubernetes集群已过期的证书.md)。

## Pod无法创建，报错详情：no providers available to validate pod request

-   如果您没有自定义PSP，出现该报错是因为您把默认PSP删除了，恢复使用默认的PSP规则即可。具体操作，请参见[使用Pod安全策略](/intl.zh-CN/Kubernetes集群用户指南/安全/基础设施安全/使用Pod安全策略.md)。
-   如果您需要自定义PSP，具体操作，请参考[使用PSP安全策略](/intl.zh-CN/Kubernetes集群用户指南/安全/容器应用安全/使用PSP安全策略.md)。

## Secret在新建的Namespace下面无法使用

Secret是命名空间级别的，您需要在新建的Namespace下新建Secret。

## 无法挂载default-token

无法挂载default-token，具体报错信息如下：

```
Normal Scheduled 13m default-scheduler Successfully assigned dev/alibaba-demo-67fcdbfb8-zklnp to cn-hangzhou.10.7.3.16   Warning FailedMount 13m (x2 over 13m) kubelet, cn-hangzhou.10.7.3.16 MountVolume.SetUp failed for volume 'default-token-8twx9' : mount failed: exit status 1 Mounting command: systemd-run Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/pods/62d39b35-9a4d-11ea-9870-c24d56a0e904/volumes/kubernetes.io~secret/default-token-8twx9 --scope -- mount -t tmpfs tmpfs /var/lib/kubelet/pods/62d39b35-9a4d-11ea-9870-c24d56a0e904/volumes/kubernetes.io~secret/default-token-8twx9 Output: Failed to start transient scope unit: Argument list too long   Warning FailedCreatePodContainer 3m40s (x49 over 13m) kubelet, cn-hangzhou.10.7.3.16 unable to ensure pod container exists: failed to create container for [kubepods burstable pod62d39b35-9a4d-11ea-9870-c24d56a0e904] : Argument list too long
```

Systemd版本太老旧。

-   升级Systemd，具体操作，请参见[systemd](https://github.com/kubernetes/kubernetes/issues/57345#issuecomment-427349235)。
-   执行`systemctl daemon-reload`命令重启清零。更多信息，请参见[systemd](https://github.com/kubernetes/kubernetes/issues/57345#issuecomment-356179385)。

## 审计日志查询方法

**RBAC相关变更操作的审计日志查询方法**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理信息页面，选择**集群资源**页签，单击日志服务Project右侧的链接。

5.  在**日志存储** \> **日志库**页面，选择对应的 `audit-<cluster_id>`，单击**查询分析**。

    ![43](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3214188161/p265346.png)

6.  单击右上角**15分钟（相对）**，选择需要查询的时间段。

    **说明：** 时间段覆盖的范围是正常到出现问题时的这段时间，比如：3 天、7天或15天。

7.  在**查询/分析**文本框中输入以下SQL查询命令，然后单击**查询/分析**。

    ```
    requestURI: "rbac.authorization.k8s.io" not (verb: get or verb: watch) 
    ```

8.  单击![45](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3465188161/p265359.png)图标，在弹出的**日志下载**对话框中，选中**通过Cloud Shell下载**，单击**确定**。

    ![an44](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3465188161/p265358.png)


**ConfigMap相关变更操作的审计日志查询方法**

在**查询/分析**文本框中输入以下SQL查询命令，然后单击**查询/分析**。更多操作，请参见[审计日志查询方法](#section_zhr_qfz_ofq)。

```
requestURI: "configmaps" and <configmap\_name\> not (verb: get or verb: watch or verb: list) 
```

**说明：** 上述查询命令在查询时，需要将<configmap\_name\>替换为实际的Deployment名称。

**Deployment的Pod扩缩容相关操作的审计日志查询方法**

在**查询/分析**文本框中输入以下SQL查询命令，然后单击**查询/分析**。更多操作，请参见[审计日志查询方法](#section_zhr_qfz_ofq)。

```
requestURI: deployments and (verb: update or verb: patch) and replicas and deployments and <deployment\_name\> not deployment-controller
```

**说明：** 上述查询命令在查询时，需要将<deployment\_name\>替换为实际的Deployment名称。

