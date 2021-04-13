---
keyword: [Secret的落盘加密, ACK@Edge Pro版集群, 阿里云密钥管理服务（KMS）]
---

# 使用阿里云KMS进行Secret的落盘加密

在ACK@Edge Pro版集群中，您可以使用在阿里云密钥管理服务KMS（Key Management Service）中创建的密钥加密Kubernetes Secret密钥。本文主要介绍如何使用阿里云密钥管理服务（KMS）中管理的密钥对ACK@Edge Pro版集群中的Kubernetes Secret密钥数据进行落盘加密。

-   在KMS控制台已创建用户主密钥，详细介绍请参见[管理密钥]()。

    **说明：** 当前开启边缘Pro版集群的Secret落盘加密只支持使用Aliyun\_AES\_256类型的主密钥，同时不支持开启自动轮转周期。

-   主账号需要授权容器服务账号使用AliyunCSManagedSecurityRole系统角色的权限。如果您使用的账号未授权，在创建边缘Pro版集群或修改已有边缘Pro版集群过程中开启Secret落盘加密时，系统会提示您进行安全系统角色授权。
-   如果当前登录账号是子账号，请确保该子账号有AliyunKMSCryptoAdminAccess系统权限，授权流程请参考[为RAM用户授权](/cn.zh-CN/用户管理/为RAM用户授权.md)。

在Kubernetes集群中，我们通常使用Secrets密钥模型存储和管理业务应用涉及的敏感信息，比如应用密码、TLS证书、Docker镜像下载凭据等敏感信息。Kubernetes会将所有的这些Secrets密钥对象数据存储在集群对应的etcd中。更多关于密钥的信息，请参见[Secrets](https://kubernetes.io/docs/concepts/configuration/secret/)。

在ACK@Edge Pro版集群中，您可以使用在密钥管理服务（KMS）中创建的密钥加密Kubernetes Secret密钥，加密过程基于Kubernetes提供的[KMS Encryption Provider机制](https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/)，使用信封加密的方式对存储在etcd中的Kubernetes Secret密钥进行自动加密和解密，信封加密的详细介绍请参见[什么是信封加密](/cn.zh-CN/常见问题/什么是信封加密？.md)，以下介绍Kubernetes Secret密钥进行加密和解密的过程：

-   当一个业务密钥需要通过Kubernetes Secret API存储时，数据会首先被API Server生成的一个随机的数据加密密钥进行加密，然后该数据密钥会被指定的阿里云KMS密钥加密为一个密文密钥存储在etcd中。
-   解密Kubernetes Secret密钥时，系统会首先调用阿里云KMS服务的解密OpenAPI进行密文密钥的解密，然后使用解密后的明文密钥对Secret数据解密并最终返回给用户。

## 在新建的ACK@Edge Pro版集群中开启Secret落盘加密

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击页面右上角的**创建集群**。

4.  单击**ACK边缘托管版**页签。

5.  在**ACK边缘托管版**页签找到**Secret落盘加密**，选中**选择KMS密钥**，在下拉框中选择KMS密钥ID。关于创建ACK@Edge Pro版集群的其他配置信息，请参见[创建ACK@Edge Pro版集群（邀测）](/cn.zh-CN/边缘容器服务ACK@Edge用户指南/ACK@Edge Pro版集群/创建ACK@Edge Pro版集群（邀测）.md)。

    ![集群](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5295659951/p133261.png)

    登录[操作审计控制台](https://actiontrail.console.aliyun.com)，在左侧导航栏单击**事件查询**，在事件查询页面有使用aliyuncsmanagedsecurityrole系统角色的加密和解密事件日志，则说明该集群后台已成功开启Secret落盘加密特性。

    ![结果验证](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5295659951/p133314.png)


## 在已创建的ACK@Edge Pro版集群中开启Secret落盘加密

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面单击目标边缘Pro版集群名称。

4.  在集群详情页面单击**基本信息**页签，在**基本信息**区域中打开**Secret落盘加密**开关。

    **说明：** 如果当前登录用户为子账号，请确保该子账号对该集群有RBAC的管理员或运维人员权限，授权流程请参考[配置RAM用户RBAC权限](/cn.zh-CN/Kubernetes集群用户指南/授权/配置RAM用户RBAC权限.md)。

    当集群状态由**更新中**变为**运行中**时，说明该集群Secret落盘加密的特性已变更完成。


## 在已创建的ACK@Edge Pro版集群中关闭Secret落盘加密

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面单击目标边缘Pro版集群名称。

4.  在集群详情页面单击**基本信息**页签，在**基本信息**区域中关闭**Secret落盘加密**开关。

    **说明：** 如果当前登录用户为子账号，请确保该子账号对该集群有RBAC的管理员或运维人员权限，授权流程请参考[配置RAM用户RBAC权限](/cn.zh-CN/Kubernetes集群用户指南/授权/配置RAM用户RBAC权限.md)。

    当集群状态由**更新中**变为**运行中**时，说明该集群Secret落盘加密的特性已变更完成。


