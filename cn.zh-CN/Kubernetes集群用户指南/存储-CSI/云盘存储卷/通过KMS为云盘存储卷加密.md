---
keyword: [阿里云KMS, 云盘存储卷加密]
---

# 通过KMS为云盘存储卷加密

数据加密适用于有高安全性或合规性要求的应用场景，您无需自建和维护密钥管理基础设施，通过加密保护存储在阿里云ECS上的数据就能保护数据的隐私性和自主性。本文介绍如何使用阿里云密钥管理服务KMS（Key Management Service）中管理的密钥对集群中的云盘存储卷数据加密。

-   已创建好一个Kubernetes集群，并且在该集群中安装CSI插件。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已开通密钥管理服务。具体操作，请参见[开通密钥管理服务](/cn.zh-CN/快速入门/开通密钥管理服务.md)。
-   已通过kubectl连接Kubernetes集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)

## 功能介绍

ECS加密采用行业标准的AES-256加密算法，利用密钥加密云盘。密钥可以是服务密钥和普通密钥（BYOK）。在加密解密的过程中，实例的性能几乎没有衰减。

-   使用加密的系统盘（或镜像）创建ECS实例后，ECS实例操作系统内数据会被自动加密，并在读取数据时自动解密。具体步骤，请参见[加密系统盘](/cn.zh-CN/块存储/加密云盘/加密系统盘.md)。
-   创建加密的数据盘并将其挂载到ECS实例后，以下数据会被自动加密，并在读取数据时自动解密。具体步骤，请参见[加密数据盘](/cn.zh-CN/块存储/加密云盘/加密数据盘.md)。
    -   加密云盘中的静态数据。
    -   加密云盘和实例间传输的数据（不包括操作系统内的数据）。
    -   加密云盘从实例传递到后端存储集群的数据。
    -   从加密云盘创建的所有快照，并且该快照的加密密钥与云盘的加密密钥保持相同。
    -   从加密快照创建的所有云盘。

## 使用限制

-   支持加密的数据盘包括ESSD云盘、SSD云盘、高效云盘和普通云盘。
-   支持加密的系统盘包括ESSD云盘、SSD云盘和高效云盘。
-   不支持加密本地盘。
-   加密系统盘时，仅支持在复制自定义镜像时完成加密。加密后，不支持以下操作：
    -   转换加密镜像为非加密镜像。
    -   跨地域复制加密镜像。
    -   共享加密镜像。
    -   导出加密镜像。
-   非加密云盘不能直接转换成加密云盘。
-   加密云盘不能直接转换为非加密云盘。

## 配置加密云盘数据卷

在创建云盘时才可以启用云盘加密，而挂载或卸载云盘时并不需要有加密相关的配置和操作。

1.  配置StorageClass参数。

    1.  将以下内容复制到sc-kms.yaml文件中。

        ```
        apiVersion: storage.k8s.io/v1
        kind: StorageClass
        metadata:
          name: csi-disk-encrypted
        provisioner: diskplugin.csi.alibabacloud.com
        parameters:
            fsType: ext4
            type: cloud_ssd
            encrypted: "true"
            kmsKeyId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        reclaimPolicy: Delete
        ```

        **说明：**

        -   `encrypted`：配置创建的云盘是否为加密云盘，`true`表示生成加密云盘。
        -   `kmsKeyId`：表示创建加密云盘使用的KMS密钥，不配置时会使用默认的CMK；当`encrypted`为`false`时，此配置不生效。
    2.  执行以下命令创建StorageClass。

        ```
        kubectl create -f sc-kms.yaml
        ```

    3.  执行以下命令查看生成的StorageClass。

        ```
        kubectl get sc csi-disk
        ```

        预期输出：

        ```
        NAME       PROVISIONER                       AGE
        csi-disk   diskplugin.csi.alibabacloud.com   9m5s
        ```

2.  创建PVC。

    1.  将以下内容复制到sc-pvc.yaml文件中。

        ```
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
          name: disk-pvc
        spec:
          accessModes:
          - ReadWriteOnce
          resources:
            requests:
              storage: 20Gi
          storageClassName: csi-disk
        ```

    2.  执行以下命令创建PVC。

        ```
        kubectl create -f sc-pvc.yaml
        ```

    3.  执行以下命令查看创建的PVC。

        ```
        kubectl get pvc
        ```

        预期输出：

        ```
        NAME             STATUS   VOLUME                        CAPACITY   ACCESS MODES   STORAGECLASS   AGE
        disk-pvc         Bound    d-wz92s6d95go6ki9x****        25Gi       RWO            csi-disk       10m
        ```

    4.  执行以下命令查看PV。

        ```
        kubectl get pv
        ```

        预期输出：

        ```
        NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                STORAGECLASS   REASON   AGE
        d-wz92s6d95go6ki9x****   25Gi       RWO            Retain           Bound    default/disk-pvc     csi-disk                10m
        ```

        从以上预期输出可得，创建的云盘ID为：d-wz92s6d95go6ki9x\*\*\*\*。

3.  查看加密云盘是否生效。

    1.  登录[ECS管理控制台](https://ecs.console.aliyun.com)。

    2.  在左侧导航栏，选择**存储与快照** \> **云盘**。

    3.  在云盘页面可看到d-wz92s6d95go6ki9x\*\*\*\*云盘为**已加密**状态。


