---
keyword: [Migrate Controller, 安装和使用]
---

# 安装Migrate Controller

Migrate Controller是ACK基于开源项目Velero开发的一个Kubernetes应用迁移的组件。本文介绍如何安装和使用Migrate Controller。

Velero是一个云原生的集群应用备份、恢复和迁移工具。Velero采用GO语言编写，可以安全地备份、恢复和迁移Kubernetes集群中的应用及其持久化存储卷。有关Velero源码项目地址，请参见[velero](https://github.com/vmware-tanzu/velero)。有关Velero Plugin for Alibaba Cloud源码项目地址，请参见[velero-plugin](https://github.com/AliyunContainerService/velero-plugin)。

**说明：** Velero的运行环境要求Kubernetes集群版本大于v1.10。

## 步骤一：完成组件安装前的准备工作

由于注册集群中的集群节点可能位于IDC内，也可能位于其他云厂商的托管平台上，在注册集群中安装Migrate-Controller组件时需要配置AK信息。您需要完成以下操作：

1.  创建RAM用户并为RAM用户创建AK。

    在注册集群中安装组件前，您需要在接入集群中设置AK用来访问云服务的权限。设置AK前，您需要创建RAM用户并为其添加访问相关云资源的权限。

    1.  创建RAM用户。

        有关如何创建RAM用户的具体步骤，请参见[创建RAM用户](/intl.zh-CN/用户管理/创建RAM用户.md)。

    2.  创建权限策略。

        有关创建权限策略的具体操作步骤，请参见[创建自定义策略](/intl.zh-CN/权限策略管理/自定义策略/创建自定义策略.md)。

        Velero组件权限策略信息如下。

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

        例如，如果只授权RAM用户OSS Bucket mybackups的读写权限，那么RAM的权限策略为：

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

        有关更多OSS细粒度授权配置，请参见[使用RAM对OSS进行权限管理](/intl.zh-CN/教程/使用RAM对OSS进行权限管理.md)。

    3.  为RAM用户添加权限。

        有关如何为RAM用户授权的具体步骤，请参见[为RAM用户授权](/intl.zh-CN/用户管理/为RAM用户授权.md)。

    4.  为RAM用户创建AK。

        有关如何为RAM用户创建AK，请参见[获取AccessKey]()。

2.  在注册集群中创建Secret。

    为了保证您的AK信息只在您的注册集群内安全使用，您需要先在注册集群中使用AK信息部署一个名为alibaba-addon-secret的Secret资源，以降低泄露风险，。对于Migrate Controller组件，ACK继承Velero开源社区的使用习惯，将其全部安装在名为velero的命名空间下，所以您需要先创建这个命名空间（如果它不存在的话），然后在这个命名空间下引用AK信息创建alibaba-addon-secret的Secret资源。命令如下：

    ```
    kubectl create ns velero
    kubectl -n velero create secret generic alibaba-addon-secret --from-literal='access-key-id=<your AccessKey ID>' --from-literal='access-key-secret=<your AccessKey Secret>'
    ```

    **说明：**

    您需要将上述代码中`<your AccessKey ID>`和`<your AccessKey Secret>`替换为您在[步骤1中创建AK](#substep_onn_mg9_lx0)获取的AK信息。

3.  在注册集群中安装Velero客户端。

    Velero组件提供了一个功能强大的客户端二进制文件，可以帮助您简单且高效地向Velero服务端提交备份或恢复任务。

    您需要在一个可以使用kubeconfig文件访问您注册集群的机器上安装Velero客户端。请根据您机器的操作系统类型 [下载velero客户端](https://github.com/vmware-tanzu/velero/releases)。


完成上述前提操作后，您可以按照以下步骤安装和使用Migrate Controller组件（Velero服务端）。

## 步骤二：安装Migrate Controller组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面，选择目标集群，并在目标集群右侧**操作**列下，选择**更多** \> **系统组件管理**。

4.  在**组件管理**页面，找到**migrate-controller**组件，然后单击**安装**。


## 步骤三：配置OSS Bucket存储空间

在Velero中，需要创建BackupStorageLocation自定义资源用于配置Velero备份的存储位置。

1.  创建名为BackupStorageLocation.yaml文件，然后拷贝以下信息至文件中。

    ```
    apiVersion: velero.io/v1
    kind: BackupStorageLocation
    metadata:
      labels:
        component: velero
      name: default
      namespace: velero
    spec:
      config:
        region: cn-hangzhou
      objectStorage:
        bucket: velero-bucket
      provider: alibabacloud
    ```

    参数描述如下：

    **说明：**

    -   设置`spec.config.region`为OSS Bucket所在地域的信息。
    -   设置`spec.objectStorage.bucket`为OSS Bucket名称。
    -   如果您的环境可以使用OSS内网端点进行数据传输，则可以添加`spec.config.network: internal`，然后ACK默认使用公网传输。
    -   如果您想要使用OSS Bucket下一个指定的子目录，则可以添加`spe.objectStorage.prefix: <your prefix>`，然后ACK默认使用Bucket根目录。
2.  执行以下命令创建BackupStorageLocation。

    ```
    kubectl apply -f BackupStorageLocation.yaml
    ```


**相关文档**  


[migrate-controller](/intl.zh-CN/产品发布记录/组件介绍与变更记录/其他/migrate-controller.md)

[使用Migrate Controller备份和恢复应用](/intl.zh-CN/Kubernetes集群用户指南/多云混合云/组件管理/使用Migrate Controller备份和恢复应用.md)

[使用Migrate Controller跨集群迁移应用](/intl.zh-CN/Kubernetes集群用户指南/多云混合云/组件管理/使用Migrate Controller跨集群迁移应用.md)

