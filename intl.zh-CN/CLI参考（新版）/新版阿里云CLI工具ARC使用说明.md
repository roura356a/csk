---
keyword: [ARC, AlibabaCloud Resourcification CLI, 阿里云CLI工具]
---

# 新版阿里云CLI工具ARC使用说明

阿里云资源化CLI（AlibabaCloud Resourcification CLI，简称ARC）是阿里云面向资源化的全新CLI环境，包含多个命令行工具。本文介绍ARC支持的命令行工具、ARC选项参数、如何使用ARC。

## 背景信息

旧版阿里云CLI工具不支持结构化参数，使用成本高，对用户不友好。新版阿里云CLI工具 ARC是以资源为视角的CLI工具，提供交互式输入，提升参数输入的体验。更多信息，请参见[alibabacloud-resourcify-cli](https://github.com/aliyun/alibabacloud-resourcify-cli)和[发布版本](https://github.com/aliyun/alibabacloud-resourcify-cli/releases)。

## 安装ARC

使用ARC前，您需要安装ARC。操作步骤如下：

1.  安装Node.js环境。
    -   macOS操作系统

        您有以下两种安装Node.js的方式：

        -   使用源码包安装。

            在官方网站[下载PKG安装包](https://nodejs.org/en/download/)。

        -   使用Homebrew工具安装。

            安装命令为`brew install node`。

    -   Windows操作系统

        在官方网站[下载MSI安装包](https://nodejs.org/en/download/)。

2.  执行以下命令安装ARC。

    ```
    npm i @alicloud/arc -g
    ```


## ARC支持的命令行工具

当成功安装ARC后，ARC将会提供以下三个命令行工具：

-   arc-tool：阿里云资源化命令行工具，用于配置、自动补全等辅助设置。例如：设置用户AK。

    命令格式：`arc-tool <command> [subCommand] [options]`

-   arc：阿里云资源化命令行工具，用于云服务资源操作。例如：创建VPC。

    命令格式：`arc <product> <resource> <action> [options]`

-   arc-cs：arc子命令，专门针对阿里云容器服务Kubernetes版集群操作的工具。

    命令格式：`arc-cs <resource> <action> [options]`

    **说明：** 尖括号`<>`中的内容为必填，方括号`[]`中的内容为选填。

    arc-cs子命令语法显示如下。

    ![arccs](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5862000161/p210946.png)


## ARC选项说明

ARC中选项参数说明如下：

-   `--profile`： 指定arc工具要使用的配置文件。
-   `-i` \| `--interaction`：命令行交互式模式，根据提示完成资源信息的输入从而完成对集群资源的操作。
-   `--region`：指定阿里云地域，后续对云资源的操作将发生在该地域。

完整的ARC命令行界面如下。

```
usage:
    arc  [子命令]
    arc  [选项]

  阿里云资源化命令行工具，用于云服务资源操作

子命令:
    cs                                  容器服务

选项:
  --profile                             [string]            指定要使用的配置文件
  --region                              [string]            指定阿里云区域
  -i,--interaction                      [boolean]           交互式填充参数
```

## 使用ARC

本文以创建阿里云ACK集群资源为例，介绍如何快速上手ARC。

1.  配置AK。

    在开始使用ARC之前，您需要为其配置AccessKey以及地域信息。

    -   方法一：通过交互式模式设置AK、地域以及语言。

        执行以下命令配置交互式信息。

        ```
        arc-tool config
        ```

        输出以下交互式内容：

        ```
        ? 凭证ID
        access-key-id access_key_id
        ? 凭证密钥
        access-key-secret access_key_secret
        ? 请选择可选配置或结束配置 region
        ? 阿里云区域
        region cn-beijing
        ? 请选择可选配置或结束配置 language
        ? CLI语言
        language en
        ? 是否执行 显示并执行命令
        arc-tool config  --access-key-id access_key_id --access-key-secret access_key_secret --region cn-beijing --language en
        ```

    -   方法二：通过命令行方式设置AK、地域以及语言。

        示例代码如下：

        ```
        arc-tool config set access_key_id <value>
        arc-tool config set access_key_secret <value>
        arc-tool config set region ssss <value>
        arc-tool config set language <value>
        ```

2.  创建集群。
    -   方法一：通过交互模式创建集群。

        执行以下命令使用`-i`开启交互模式。

        ```
        arc-cs cluster create-managed -i
        ```

        命令行界面输出的交互式内容如下：

        ```
        ? 以下选项具有冲突，请选择其中一项。 login-password
        ? SSH登录密码。密码规则为8~30个字符，且至少同时包含三项（大小写字母、数字和特殊符号）。
        login-password Hello1234
        ? 集群名称， 集群名称可以使用大小写英文字母、中文、数字、中划线。
        name cluster-by-arc
        交换机ID。List长度范围为[1,3]
        ? 交换机ID。List长度范围为[1,3]
        vswitch-ids.0 vsw-bp1910rnb01vtrvfzek5w
        ? 是否继续配置第2个。 vswitch-ids No
        Worker节点实例规格，可以配置多个规格。
        ? Worker节点实例规格，可以配置多个规格。
        worker-instance-types.0 ecs.g6a.xlarge
        ? 是否继续配置第2个。 worker-instance-types Yes
        ? Worker节点实例规格，可以配置多个规格。
        worker-instance-types.1 ecs.g6.xlarge
        ? 是否继续配置第3个。 worker-instance-types No
        ? Worker节点数。范围是[0，100]
        num-of-nodes 2
        ? Worker节点系统盘类型。
        worker-system-disk-category cloud_efficiency
        ? Worker节点系统盘大小，单位为GiB。
        worker-system-disk-size 120
        ? 集群使用的VPC。
        vpcid vpc-bp1qjw8u2gk7jeipp2n21
        ? Service网络的网段，不能和VPC网段及Pod网络网段冲突。当选择系统自动创建VPC时，默认使用172.19.0.0/20网段。
        service-cidr 172.19.0.0/20
        ? 请选择可选配置或结束配置。 security-group-id | is-enterprise
        ? 以下选项具有冲突，请选择其中一项。 is-enterprise
        ? 是否创建企业安全组。
        is-enterprise true
        ? 请选择可选配置或结束配置。 region
        ? 集群所在地域ID。
        region cn-hangzhou
        ? 请选择可选配置或结束配置。 worker-instance-charge-type
        ? Worker节点付费类型:
        PrePaid：预付费
        PostPaid：按量付费
        worker-instance-charge-type PostPaid
        ? 请选择可选配置或结束配置。 os-type
        ? 运行pod的主机的操作系统类型。
        os-type Linux
        ? 请选择可选配置或结束配置。 platform
        ? 运行pod的主机的平台架构，如 x86。
        platform AliyunLinux
        ? 请选择可选配置或结束配置。 deletion-protection
        ? 是否开启集群删除保护，防止通过控制台或API误删除集群。
        deletion-protection false
        ? 请选择可选配置或结束配置 [DONE]
        ? 是否执行 显示并执行命令
        arc-cs cluster create-managed  --login-password Hello**** --name cluster-by-arc --vswitch-ids '["vsw-bp1910rnb01vtrvfz****"]' --worker-instance-types '["ecs.g6a.xlarge","ecs.g6.xlarge"]' --num-of-nodes 2 --worker-system-disk-category cloud_efficiency --worker-system-disk-size 120 --vpcid vpc-bp1qjw8u2gk7jeipp**** --service-cidr 172.19.0.0/20 --is-enterprise true --region cn-hangzhou --worker-instance-charge-type PostPaid --os-type Linux --platform AliyunLinux --deletion-protection false
        ```

    -   方法二：通过命令行方式创建集群。

        示例命令如下：

        ```
        arc-cs cluster create-managed  --login-password Hello**** --worker-instance-types '["ecs.g6a.xlarge"]' --num-of-nodes 2 --worker-system-disk-category cloud_essd --worker-system-disk-size 120 --vpcid vpc-bp1qjw8u2gk7jeipp**** --name test-cli2 --region cn-hangzhou --container-cidr 172.22.0.0/16 --service-cidr 172.19.0.0/20 --vswitch-ids '["vsw-bp1910rnb01vtrvfz****"]'
        ```


## 通过ARC操作ACK

您可以使用ARC操作ACK，常见操作的示例如下表。

|场景示例|CLI命令|描述|适用范围|
|:---|-----|:-|:---|
|[查看所有集群](/intl.zh-CN/CLI参考（新版）/查看所有集群.md)|`arc-cs cluster list`|查看您在容器服务中创建的所有集群。|ACK支持的Kubernetes集群。|
|[查看集群详细信息](/intl.zh-CN/CLI参考（新版）/查看集群详细信息.md)|`arc-cs cluster get <clusterId>`|根据集群ID，查看集群的详细信息。|ACK支持的Kubernetes集群。|
|[创建专有版集群](/intl.zh-CN/CLI参考（新版）/创建集群/创建专有版集群.md)|`arc-cs cluster create-kubernetes`|创建一个专有版集群，并新建指定数量的节点。|ACK支持的专有版Kubernetes集群。|
|[创建托管版集群](/intl.zh-CN/CLI参考（新版）/创建集群/创建托管版集群.md)|`arc-cs cluster create-managed`|创建一个托管版集群。|ACK支持的托管版Kubernetes集群。|
|[创建Serverless集群](/intl.zh-CN/CLI参考（新版）/创建集群/创建Serverless集群.md)|`arc-cs cluster create-ask`|创建一个Serverless版集群。|ACK支持的Serverless版Kubernetes集群。|
|[扩容集群](/intl.zh-CN/CLI参考（新版）/扩容集群.md)|`arc-cs cluster scaleout <clusterId>`|增加集群中节点的数量。|ACK支持的Kubernetes集群。|
|[添加已有ECS实例](/intl.zh-CN/CLI参考（新版）/添加已有ECS实例.md)|`arc cs node attach <clusterid> --password <password> --instances '["<instanceid1>","<instanceid2>"]'`|添加已有ECS实例至ACK集群。|ACK支持的Kubernetes集群。|
|[删除集群](/intl.zh-CN/CLI参考（新版）/删除集群.md)|`arc-cs cluster delete <clusterId>`|根据集群ID，删除集群实例，并释放集群所有节点资源。|ACK支持的Kubernetes集群。|

