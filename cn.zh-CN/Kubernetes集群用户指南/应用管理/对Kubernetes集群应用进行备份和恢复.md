---
keyword: [应用备份, 应用恢复, 跨集群恢复]
---

# 对Kubernetes集群应用进行备份和恢复

ACK基于开源项目Velero开发了应用备份功能。该功能支持ACK集群和外部注册集群对应用和存储卷进行单集群和跨集群的备份和恢复。本文介绍如何对Kubernetes应用进行备份及恢复，以便于对您的集群应用及数据进行有效的保护。

-   该功能处于公测期。使用该功能前，您需要[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)[提交工单](https://workorder-intl.console.aliyun.com/console.htm)提交工单申请白名单。
-   已创建Kubernetes集群。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)、[创建Kubernetes专有版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes专有版集群.md)或[注册外部Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/多云混合云管理/注册外部Kubernetes集群.md)。

    **说明：** 请确保您的Kubernetes集群版本大于1.10，否则影响该功能的使用。

-   已创建OSS Bucket。具体操作，请参见[创建存储空间](/cn.zh-CN/控制台用户指南/存储空间管理/创建存储空间.md)。

随着越来越多的应用运行在Kubernetes中，对应用进行定时的备份就显得格外重要。应用备份可以有效的防护由于意外情况导致服务长时间中断而无法恢复的情况。区别于传统的备份单机、备份磁盘的方案，基于Kubernets的应用备份，主要关注于运行在Kubernets中的应用及其数据、资源对象、配置及整个命名空间等。

## 使用限制

-   仅支持按命名空间备份应用。
-   支持对应用数据进行全量备份，暂不支持增量备份。
-   备份应用时，正在被删除的资源不会被备份。

## 步骤一：配置OSS权限

**为托管版或专有版集群配置权限**

如果您的集群为托管版集群或专有版集群，您需要为托管版集群或专有版集群配置OSS权限。

1.  为OSS创建自定义权限策略。具体步骤，请参见[创建自定义策略](/cn.zh-CN/权限策略管理/自定义策略/创建自定义策略.md)。

    **说明：** 更多OSS细粒度授权配置信息，请参见[使用RAM对OSS进行权限管理](/cn.zh-CN/教程/使用RAM对OSS进行权限管理.md)。

    如果您想要所有OSS的权限，请按以下示例设置权限策略。

    ```
    {
        "Version": "1",
        "Statement": [
            {
                "Action": [
                    "oss:PutObject",
                    "oss:GetObject",
                    "oss:DeleteObject",
                    "oss:GetBucket",
                    "oss:ListObjects",
                    "oss:ListBuckets"
                ],
                "Resource": [
                    "*"
                ],
                "Effect": "Allow"
            }
        ]
    }
    ```

    如果您只想要指定OSS的读写权限，请按以下示例设置权限策略。

    ```
    {
        "Version": "1",
        "Statement": [
            {
                "Action": [
                    "oss:PutObject",
                    "oss:GetObject",
                    "oss:DeleteObject",
                    "oss:GetBucket",
                    "oss:ListObjects",
                    "oss:ListBuckets"
                ],
                "Resource": [
                    "acs:oss:*:*:mybackups",
                    "acs:oss:*:*:mybackups/*"
                ],
                "Effect": "Allow"
            }
        ]
    }
    ```

    您需要将上述代码中的`mybackups`替换为您自己的OSS名称。

2.  为托管集群RAM角色添加权限。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)[容器服务管理控制台](https://partners-intl.console.aliyun.com/#/cs)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群信息页面单击**集群资源**页签，查看Worker RAM角色。

    5.  登录[RAM控制台](https://ram.console.aliyun.com/)[RAM控制台](https://partners-intl.console.aliyun.com/#/ram)。

    6.  在控制台左侧导航栏单击**授权**。

    7.  在授权页面单击**新增授权**，在**添加权限**对话框配置参数，然后单击**确定**。

        |参数|说明|
        |--|--|
        |授权范围|可选**云账号全部资源**和**指定资源组**。|
        |被授权主体|输入Worker RAM角色。|
        |选择权限|单击**自定义策略**，在搜索框中搜索步骤[1](#step_csd_xek_3x4)中创建的权限策略，然后单击目标权限策略名称。|


**为注册集群配置权限**

如果您的集群为注册集群，您需要创建RAM用户，为RAM用户添加访问云资源权限，然后创建AccessKey。

1.  创建RAM用户。具体操作，请参见[创建RAM用户](/cn.zh-CN/用户管理/创建RAM用户.md)。

2.  为OSS创建自定义权限策略。具体操作，请参见步骤[1](#step_csd_xek_3x4)。

3.  为RAM用户添加权限。具体操作，请参见[为RAM用户授权](/cn.zh-CN/用户管理/为RAM用户授权.md)。

4.  为RAM用户创建AccessKey。具体操作，请参见[获取AccessKey]()。

5.  在注册集群中创建Secret。

    为了保证您的AccessKey信息只在您的注册集群内安全使用，您需要先在注册集群中使用AccessKey信息部署一个名为alibaba-addon-secret的Secret资源，以降低泄露风险。

    例如，对于Migrate Controller组件，ACK继承Velero开源社区的使用习惯，将其全部安装在名为velero的命名空间下。所以您需要先创建这个命名空间（如果velero的命名空间不存在），然后在这个命名空间下引用AccessKey信息创建alibaba-addon-secret的Secret资源。

    1.  执行以下命令，创建velero命名空间。

        ```
        kubectl create ns velero
        ```

    2.  执行以下命令，创建名为alibaba-addon-secret的Secret资源。

        ```
        kubectl -n velero create secret generic alibaba-addon-secret --from-literal='access-key-id=<your AccessKey ID>' --from-literal='access-key-secret=<your AccessKey Secret>'
        ```

        您需要将上述代码中`your AccessKey ID`和`your AccessKey Secret替`换为您在步骤[4](#step_o2f_lop_eli)中获取的AccessKey信息。


## 步骤二：安装应用备份组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)[容器服务管理控制台](https://partners-intl.console.aliyun.com/#/cs)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏选择**运维管理** \> **应用备份（公测）**。

5.  在应用备份页面单击**开始安装**。

    **说明：** 应用备份组件安装完之后，会生成velero的命名空间，在使用备份过程中，请勿删除该命名空间。

    安装完成后，显示以下界面，说明应用备份组件安装成功。

    ![应用备份组件](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6875260161/p214169.png)


## 步骤三：创建备份仓库

当前ACK应用备份使用阿里云OSS来对备份的数据进行存储。接下来为您介绍接入OSS基本流程。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)[容器服务管理控制台](https://partners-intl.console.aliyun.com/#/cs)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏选择**运维管理** \> **应用备份（公测）**。

5.  在应用备份页面**备份仓库**页签下单击**创建**。

6.  在**创建**对话框配置参数，然后单击**确定**。

    |参数|说明|
    |--|--|
    |名称|备份仓库的名称，支持小写字母和数字。|
    |Bucket区域|OSS Bucket所在地域。|
    |Bucket名称|OSS Bucket的名称。|
    |Bucket子目录|OSS Bucket的子目录\(非必填\)。|
    |网络模式|支持**公网**和**内网**。如果您的环境可以使用OSS内网端点进行数据传输，则选择内网，ACK默认使用公网传输。|


## 步骤四：创建备份任务

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)[容器服务管理控制台](https://partners-intl.console.aliyun.com/#/cs)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏选择**运维管理** \> **应用备份（公测）**。

5.  在应用备份页面单击**备份**页签。

6.  在**备份**页签单击**创建**。

7.  如果您备份的是存储卷，需要执行以下命令，为对应的Pod的数据卷增加注解。

    **说明：** 如果您备份的是应用，可以跳过此步骤。

    ```
    kubectl -n 要备份的ns名称 annotate pod/备份的pod名字 backup.velero.io/backup-volumes=该pod中对应pvc的名字
    ```

8.  在**创建**对话框设置**名称**、**备份仓库**、**备份命名空间**和**有效期**，然后单击**确定**。

    **说明：** 备份任务名称仅支持小写字母和数字。

    在**备份**页签下创建的备份任务状态显示**Completed**，表示该任务备份成功。


## 步骤五：创建恢复任务

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)[容器服务管理控制台](https://partners-intl.console.aliyun.com/#/cs)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏选择**运维管理** \> **应用备份（公测）**。

5.  在应用备份页面单击**恢复**页签。

6.  在**恢复**页签单击**创建恢复任务**。

7.  在**创建恢复任务**对话框输入**名称**，选择**备份名称**，然后单击**确定**。

    **说明：**

    -   恢复任务名称仅支持小写字母和数字。
    -   恢复时对于已经存在的资源不会覆盖，只会恢复当前集群中不存在的资源。如果想恢复到之前的版本，则需要删除现有资源，然后在进行恢复。
8.  在提示对话框中单击**确定**。


## 跨集群恢复应用

执行跨集群恢复应用前，您需要创建2个集群。本例中一个名为cluster\_A集群，另一个名为cluster\_B集群。接下来展示在cluster\_A集群备份应用，在cluster\_B集群恢复应用。

**说明：** 创建集群时，请确保2个集群的Kubernetes版本一致，否则会导致跨集群恢复应用失败。

![架构图](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3035242161/p223924.png)

1.  授予cluster\_A和cluster\_B集群OSS权限。具体操作，请参见[步骤一：配置OSS权限](#section_eex_psy_s4t)。

2.  在cluster\_A和cluster\_B集群中安装应用备份组件。具体操作，请参见[步骤二：安装应用备份组件](#section_7v1_q3t_hak)。

3.  在cluster\_A和cluster\_B集群创建备份仓库。创建备份仓库时，给cluster\_A和cluster\_B集群设置同一个OSS，并且选择公网访问。具体操作，请参见[步骤三：创建备份仓库](#section_jcd_lxp_dpg)。

4.  在cluster\_A集群创建备份任务。具体操作，请参见[步骤四：创建备份任务](#section_v4n_i9q_da6)。

    cluster\_A集群创建备份任务完成后，您可以在cluster\_B集群应用备份页面**备份**页签下看到cluster\_A集群的备份任务。

5.  在cluster\_B集群应用备份页签**恢复**页签下选择cluster\_A集群的备份任务，执行恢复操作。具体操作，请参见[步骤五：创建恢复任务](#section_gdu_9n6_lft)。


**相关文档**  


[使用Migrate Controller备份和恢复应用](/cn.zh-CN/Kubernetes集群用户指南/多云混合云管理/组件管理/使用Migrate Controller备份和恢复应用.md)

