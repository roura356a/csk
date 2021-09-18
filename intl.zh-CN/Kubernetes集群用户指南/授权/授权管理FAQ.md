---
keyword: 授权管理FAQ
---

# 授权管理FAQ

本文介绍授权管理过程的一些常见问题的解决方法。

-   [授权是否可以精确到应用级别？](#section_7r6_eh8_lyb)
-   [如何给RAM用户添加创建集群的权限？](#section_wrw_5cm_wsj)
-   [如何手动打开角色收敛的RAM授权页面？](#section_fdh_k13_qyn)
-   [被授权了cs:admin权限的RAM用户在K8s无法创建CRD](#section_i8z_782_b6i)
-   [页面提示没有权限，报错详情：APISERVER\_403](#section_9ft_3qt_vjq)
-   [ECS的RAM角色被删后如何恢复？](#section_q0q_oen_y5f)
-   [已经授予了RAM用户对所有集群的只读权限，但是有些集群仍然看不到](#section_x8g_crn_5ey)
-   [如何在Kubernetes集群指定RAM中的自定义角色？](#section_0sm_z8q_eyo)
-   [RAM用户如何给其他RAM用户进行RBAC授权？](#section_wb5_8oc_wnd)
-   [子账号使用Kubernetes集群出现“您没有权限进行当前操作。如果您是子账号用户，请联系主账号进行授权”报错](~~142276~~)
-   [根据错误信息判断是RAM授权问题还是RBAC授权问题](#section_fyd_gcm_3fn)

## 授权是否可以精确到应用级别？

可以。您可以通过自定义ClusterRole策略，在策略规则中的`resourceNames`字段指定相应应用实例名称实现应用级别的授权。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**授权管理**。

3.  在授权管理页面中的选择子账号配置向导中选择需要授权的RAM用户，单击**管理权限**。

    **说明：** 如果您使用RAM用户授权，请先确保该RAM用户已完成集群RBAC配置[自定义RAM授权策略](/intl.zh-CN/Kubernetes集群用户指南/授权/自定义RAM授权策略.md)文档中的RAM授权，同时已被授予集群内RBAC管理员权限或cluster-admin角色。

4.  单击**集群RBAC配置**页面的**![122](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3588687161/p261686.png)添加权限**，指定集群和命名空间，选中**自定义**角色，并在**自定义**的下拉列表中选择对应的ClusterRole名称，完成后单击**下一步**。

    ![AN13](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/2184687161/p261530.png)

    **说明：** 当前针对RAM用户的授权，支持在一个目标集群或命名空间上授予一个预置角色和多个自定义角色。

    集群和命名空间的预置角色定义可查看下面的角色权限说明：

    |角色|集群内RBAC权限|
    |--|---------|
    |管理员|对所有命名空间下所有资源的读写权限。|
    |运维人员|对所有命名空间下控制台可见Kubernetes资源的读写权限，对集群节点、存储卷、命名空间、配额的只读权限。|
    |开发人员|对所有命名空间或所选命名空间下控制台可见Kubernetes资源的读写权限。|
    |受限用户|对所有命名空间或所选命名空间下控制台可见Kubernetes资源的只读权限。|
    |自定义|权限由您所选择的ClusterRole决定，请在确定所选ClusterRole对各类资源的操作权限后再进行授权，以免RAM用户获得不符合预期的权限。详情请参见[容器服务自定义 RAM 授权策略](/intl.zh-CN/Kubernetes集群用户指南/授权/自定义RAM授权策略.mdsection_5ya_j6r_fh7)。|

    相关后续操作步骤，请参见[配置RAM用户RBAC权限](/intl.zh-CN/Kubernetes集群用户指南/授权/配置RAM用户RBAC权限.md)。


## 如何给RAM用户添加创建集群的权限？

1.  用阿里云账号进行容器服务系统角色的批量授权。

    -   服务角色的授权是一次性操作，如果您不确定是否已经完成，可以使用阿里云账号登录后打开以下链接做容器服务系统角色一次性的批量授权：[ur.alipay.com/1paTcxSWdAEW70GVH5TZiO](https://ur.alipay.com/1paTcxSWdAEW70GVH5TZiO)
    -   关于容器服务默认系统角色说明，请参见[容器服务默认角色](/intl.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.md)。
2.  主账号对目标子账号进行自定义RAM授权策略操作。

    请确保目标RAM账号至少有`cs:CreateCluster`权限。具体操作，请参见[自定义RAM授权策略](/intl.zh-CN/Kubernetes集群用户指南/授权/自定义RAM授权策略.md)。

    YAML文件模板样例如下：

    ```
    {
     "Statement": [{
         "Action": [
             "cs:*"
         ],
         "Effect": "Allow",
         "Resource": [
             "CreateCluster"
         ]
     }],
     "Version": "1"
    }
    ```

    **说明：**

    -   由于集群创建过程中需要选择关联的VPC等云资源，请根据需要进行对应的RAM授权操作。
    -   请确保目标子账号至少有VPC资源的List权限，该权限的授权可以通过授权系统策略AliyunVPCReadOnlyAccess完成。
    -   如果有其他的资源配置需要，请参考对应服务的系统策略或授权文档进行授权操作。更多信息，请参见[RAM授权](/intl.zh-CN/Kubernetes集群用户指南/授权/授权概述.md)。

## 如何手动打开角色收敛的RAM授权页面？

因为某种原因把ACK的RAM授权给删了，需要重新授权。

具体操作，请参见[步骤二：一键授权默认角色](/intl.zh-CN/Kubernetes集群用户指南/首次使用容器服务Kubernetes版.md)。

**说明：** 重新授权需要用阿里云账号操作。

## 被授权了`cs:admin`权限的RAM用户在K8s无法创建CRD

部分容器服务集群（2019年05月以前创建的集群）的默认管理员权限并不包含全部K8s资源访问权限，您可以绑定一个自定义的`cluster-admin`，或者删除集群已有`cs:admin`的ClusterRole，然后重新创建ClusterRole。

YAML文件模板样例如下：

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cs:admin
rules:
- apiGroups:
  - '*'
  resources:
  - '*'
  verbs:
  - '*'
- nonResourceURLs:
  - '*'
  verbs:
  - '*'
```

## 页面提示没有权限，报错详情：APISERVER\_403

当前RAM用户没有被授予K8s集群相应的RBAC权限，需要在**授权管理**页面对该账号进行授权。具体操作，请参见[配置RAM用户RBAC权限](/intl.zh-CN/Kubernetes集群用户指南/授权/配置RAM用户RBAC权限.md)。

## ECS的RAM角色被删后如何恢复？

ECS节点上运行的应用中请求`metadata api 100`地址时提示**404**或**Message:Node condition RAMRoleError is now: True, reason: NodeHasNoRAMRole**。您可以选择以下方案尝试恢复ECS的RAM角色：

-   ECS的RAM角色被删后，您需要为节点授予相应的RAM角色。具体操作，请参见[更换实例RAM角色](/intl.zh-CN/安全/管理身份和权限/通过RAM角色控制资源访问/管理实例RAM角色/更换实例RAM角色.md)。
    -   Master节点需要授予**集群信息** \> **集群资源** \> **Master RAM角色**的RAM角色。
    -   Worker节点需要授予**集群信息** \> **集群资源** \> **Worker RAM 角色**的RAM角色。
-   如果您修改了RAM角色的策略内容，需要确认修改的内容是否有问题。
-   如果您是在上报提示前修改了RAM角色，请尝试恢复为上报提示前RAM角色策略内容授权版本。

## 已经授予了RAM用户对所有集群的只读权限，但是有些集群仍然看不到

**问题现象**

通过RAM控制台给RAM用户授权了全部集群的只读权限，通过RBAC授予了其中两个集群指定命名空间的访问权限。之前RAM用户在控制台可以看到所有集群，然而现在只能看到部分集群。用户的RAM权限正常，且近期没有做权限变更。

**问题原因**

您没有使用对应的RAM用户登录RAM控制台或者使用了特定的资源组，切换为使用相应的RAM用户登录RAM控制台并且资源组切换为**账号全部资源**。

**解决方案**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在顶部菜单栏，选择**账号全部资源** \> **账号全部资源**。

    ![11](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9302817161/p259620.png)

3.  将鼠标移至控制台右上角头像位置，确保是子账号登录。


## 如何在Kubernetes集群指定RAM中的自定义角色？

Kubernetes集群暂不支持指定RAM中的自定义角色，但是在Kubernetes集群中创建Worker时会自动生成一个角色，您可以通过给该角色添加Policy的方式，来实现角色授权。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群信息管理页单击**集群资源**页签。

5.  在**集群资源**页面，单击**Worker RAM角色**右侧的链接。

6.  在RAM 角色管理页面，单击权限策略名称。

7.  进入策略权限管理页面，单击**修改策略内容**，将以下内容填入后，单击**确定**。本示例中授予该角色伸缩和删除集群的权限。如果您想了解更多的权限，请参见[自定义RAM授权策略](/intl.zh-CN/Kubernetes集群用户指南/授权/自定义RAM授权策略.md)。

    ```
    {
                "Action": [
                  "cs:ScaleCluster",
                  "cs:DeleteCluster"
                ],
                "Resource": "*",
                "Effect": "Allow"
             }
    ```

    ![修改权限](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/2175659951/p44694.png)


## RAM用户如何给其他RAM用户进行RBAC授权？

默认情况下，RAM用户（该例用RAM用户A描述）不具备对其他RAM用户授权RBAC的能力，需要首先确保RAM用户A在目标集群或命名空间上被授予预置的管理员角色或自定义中的cluster-admin角色，同时给RAM用户A授予RAM相应权限，策略内容包括：

-   查看其他RAM用户
-   授予RAM权限策略
-   查看RBAC权限配置
-   RBAC授权能力

1.  向RAM用户A授予RAM权限。

    登录[RAM控制台](https://ram.console.aliyun.com/)，向RAM用户A授予RAM权限，请参见[自定义RAM授权策略](/intl.zh-CN/Kubernetes集群用户指南/授权/自定义RAM授权策略.md)。

    授权策略内容示例参考：

    ```
    {
        "Statement": [{
                "Action": [
                    "ram:Get*",
                    "ram:List*",
                    "cs:GetUserPermissions",
                    "cs:GetSubUsers",
                    "cs:GrantPermission"
                ],
                "Resource": "*",
                "Effect": "Allow"
            },
            {
                "Action": [
                    "ram:AttachPolicyToUser",
                    "ram:AttachPolicy"
                ],
                "Effect": "Allow",
                "Resource":  [
                    "acs:ram:*:*:policy/xxxxxx",
                    "acs:*:*:*:user/*"
                ]
            }
        ],
        "Version": "1"
    }
                                
    ```

    **说明：** xxxxxx需要替换成您想要绑定的RAM策略名称。例如，您替换成\*，表示RAM用户A拥有所有RAM策略的授权绑定能力。

2.  RAM用户A给其他RAM用户授权。

    当RAM用户A完成了上述策略的绑定后，即拥有对其他RAM用户在指定策略范围内的RAM授权能力，以及在集群内的RBAC授权能力。您可以参见[配置RAM用户RBAC权限](/intl.zh-CN/Kubernetes集群用户指南/授权/配置RAM用户RBAC权限.md)，对其他RAM用户授权。


## 根据错误信息判断是RAM授权问题还是RBAC授权问题

可以通过API或控制台的错误信息快速判断是RAM权限缺失还是RBAC权限缺失导致的问题。

-   RAM权限缺失问题

    **问题现象**

    API或控制台上报类似下方的错误信息：

    ```
    RAM policy Forbidden for action cs:DescribeEvents
    STSToken policy Forbidden for action cs:DescribeClusterNodes
    ```

    **问题原因**

    从上述错误信息可以得出，RAM用户未授权RAM Action `cs:DescribeEvents`的权限。

    **解决方案**

    如果API或控制台的错误信息中包含RAM policy Forbidden或STSToken policy Forbidden，说明是RAM权限缺失导致的问题。请对相应的RAM权限进行授权，具体操作，请参见[自定义RAM授权策略](/intl.zh-CN/Kubernetes集群用户指南/授权/自定义RAM授权策略.md)。

-   RBAC权限缺失问题

    **问题现象**

    API或控制台上报类似下方的错误信息：

    ```
    events is forbidden: User "<uid>" cannot list resource "events" in API group "" at the cluster scope
    ForbiddenQueryClusterNamespace, Forbidden query namespaces
    ```

    **问题原因**

    从上述错误信息可以得出，用户`<uid>`未被授权列出资源事件的权限。

    **解决方案**

    如果API或控制台的错误信息中包含APISERVER\_403、User "xxx" cannot xx resource "xx" in API group或ForbiddenQueryClusterNamespace，说明是RBAC权限缺失导致的问题。请对相应角色添加对应集群内RBAC权限，具体操作，请参见[配置RAM用户RBAC权限](/intl.zh-CN/Kubernetes集群用户指南/授权/配置RAM用户RBAC权限.md)。


