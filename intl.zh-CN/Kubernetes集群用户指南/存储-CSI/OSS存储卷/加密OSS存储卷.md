---
keyword: [阿里云KMS, OSS存储卷加密]
---

# 加密OSS存储卷

数据加密适用于有高安全性或合规性要求的应用场景，您无需自建和维护密钥管理基础设施，通过加密保护存储在阿里云ECS上的数据就能保护数据的隐私性和自主性。本文介绍如何使用KMS（Key Management Service）托管的CMK（Customer Master Key）或OSS完全托管密钥对OSS存储卷数据加密。

-   已创建Kubernetes集群，并且在该集群中安装CSI插件。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已创建Bucket。具体操作，请参见[创建存储空间](/intl.zh-CN/快速入门/控制台快速入门/创建存储空间.md)。

    **说明：** 相同地域的OSS Bucket可以使用私网地址进行挂载。

-   已开通密钥管理服务。具体操作，请参见[开通密钥管理服务](/intl.zh-CN/快速入门/开通密钥管理服务.md)。
-   已通过kubectl连接Kubernetes集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)

## 加密方式

阿里云容器服务只支持服务端加密，OSS存储卷加密分为服务端加密、客户端加密：

-   服务端加密：OSS将数据保存到数据中心的磁盘之前进行加密，并且在下载对象时自动进行解密。
-   客户端加密：可以使用客户端加密SDK，在本地进行数据加密，并将加密后的数据上传到OSS。

同一对象（Object）在同一时间内仅可以使用一种服务器端加密方式。OSS针对不同使用场景提供了两种服务器端加密方式，您可以根据实际使用场景选用。

-   使用KMS托管的默认CMK或指定CMK ID加密OSS Object（SSE-KMS）

    -   KMS托管的默认CMK加密OSS Object：上传对象文件时，配置`HTTP X-OSS-server-side-encryption`为`KMS`，不指定CMK ID。
    -   KMS托管的指定CMK ID加密OSS Object：上传对象文件时，配置`HTTP X-OSS-server-side-encryption`为`KMS`，指定`X-OSS-server-side-encryption-key-id`为`CMK ID`。
    **说明：** 使用KMS密钥功能时会产生少量的KMS密钥API调用费用。关于费用详情，请参考[计费说明](/intl.zh-CN/产品计费/计费说明.md)。

    数据无需通过网络发送到KMS服务端进行加解密，是一种低成本的加解密方式。

-   使用OSS完全托管的密钥加密OSS Object（SSE-OSS）
    -   基于OSS完全托管的加密方式，是Object的一种属性。
    -   OSS负责生成和管理数据加密密钥，并使用行业标准的强加密算法AES-256。
    -   上传对象文件时，配置`HTTP X-OSS-server-side-encryption`为`AES256`。

## 配置加密OSS存储卷

通过OSSFS工具配置OSS存储卷的加密参数，在挂载PV时配置生效。

OSSFS支持三种加密配置方式：

**方式一：使用KMS托管的默认CMK加密OSS Object**

1.  将以下内容复制到kms-cmk-default.yaml文件中。

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: oss-csi-pv
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteOnce
      csi:
        driver: ossplugin.csi.alibabacloud.com
        volumeHandle: oss-csi-pv
        volumeAttributes:
          bucket: "python"
          url: "oss-cn-hangzhou.aliyuncs.com"
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"
          akId: "***"
          akSecret: "***"
          path: "/"
          encrypted: "kms"
    ```

    |参数|说明|
    |--|--|
    |akId|AccessKey ID|
    |akSecret|AccessKey Secret|
    |encrypted|存储卷的加密方式|

2.  执行以下命令创建加密存储卷。

    ```
    kubectl create -f kms-cmk-default.yaml
    ```

    验证结果

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在控制台左侧导航栏中，单击**集群**。
    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
    4.  在集群管理页左侧导航栏选择**存储** \> **存储卷**。可以在存储卷页面查看到创建的加密存储卷。

**方式二：使用KMS托管的指定CMK ID加密OSS Object**

1.  将以下内容复制到kms-cmk.yaml文件中。

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: oss-csi-pv
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteOnce
      csi:
        driver: ossplugin.csi.alibabacloud.com
        volumeHandle: oss-csi-pv
        volumeAttributes:
          bucket: "python"
          url: "oss-cn-hangzhou.aliyuncs.com"
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"
          akId: "***"
          akSecret: "***"
          path: "/"
          encrypted: "kms"
          kmsKeyId: "*********"
    ```

2.  执行以下命令创建加密存储卷。

    ```
    kubectl create -f kms-cmk.yaml
    ```

    验证结果

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在控制台左侧导航栏中，单击**集群**。
    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
    4.  在集群管理页左侧导航栏选择**存储** \> **存储卷**。可以在存储卷页面查看到创建的加密存储卷。

**方式三：使用OSS完全托管的密钥加密OSS Object（SSE-OSS）**

1.  将以下内容复制到sse-oss.yaml文件中。

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: oss-csi-pv
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteOnce
      csi:
        driver: ossplugin.csi.alibabacloud.com
        volumeHandle: oss-csi-pv
        volumeAttributes:
          bucket: "python"
          url: "oss-cn-hangzhou.aliyuncs.com"
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"
          akId: "***"
          akSecret: "***"
          path: "/"
          encrypted: "aes256"
    ```

2.  执行以下命令创建加密存储卷。

    ```
    kubectl create -f sse-oss.yaml
    ```

    验证结果

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在控制台左侧导航栏中，单击**集群**。
    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
    4.  在集群管理页左侧导航栏选择**存储** \> **存储卷**。可以在存储卷页面查看到创建的加密存储卷。

