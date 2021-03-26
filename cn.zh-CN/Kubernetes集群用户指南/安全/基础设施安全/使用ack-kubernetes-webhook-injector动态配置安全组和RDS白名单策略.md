---
keyword: [k8s webhook injector, 添加安全规则, IP地址, ack-kubernetes-webhook-injector]
---

# 使用ack-kubernetes-webhook-injector动态配置安全组和RDS白名单策略

在对权限要求相对较高的云上的场景中，您需要将Pod的IP地址动态的加入或者移出指定的安全组，或者RDS白名单，以实现对权限最细粒度的控制。使用ack-kubernetes-webhook-injector，您可以通过为Pod添加注解的方式，动态地将Pod的IP加入或者移出安全组或白名单。本文介绍如何安装及使用ack-kubernetes-webhook-injector组件。

-   [创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)
-   [通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)

在云计算的使用场景中，某些资源需要配置相应的安全策略才能被外部访问，例如实例需要配置安全组的入方向放行策略，或者RDS数据库需要配置访问客户端的源IP地址白名单。虽然在创建ACK集群时可以将集群节点的IP网段加入到指定RDS的白名单中，但此方案有几个缺点：

-   白名单规则粒度太粗，加入白名单的IP网段覆盖了集群所有节点和Pod的IP地址。
-   集群删除后白名单规则需手动清理。
-   只支持RDS白名单规则，无法支持安全组规则的添加。

为了解决上述几点问题，阿里云容器服务ACK团队开发了ack-kubernetes-webhook-injector，它可以将指定Pod的IP地址动态加入RDS白名单或者安全组中，并且在Pod被删除时，将其IP从白名单或者安全组中移除，实现对安全策略的最细粒度的控制。

目前ack-kubernetes-webhook-injector支持的功能有：

-   随着Pod的创建与删除，自动将Pod的IP加入或移出指定的安全组（Security Group）。
-   随着Pod的创建与删除，自动将Pod的IP加入或移出指定的RDS白名单。

## 安装ack-kubernetes-webhook-injector

使用ack-kubernetes-webhook-injector组件前，您需要安装该组件。安装步骤如下：

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  选择并单击**ack-kubernetes-webhook-injector**。

4.  在**参数**页签，将`use_aksk`的值设置为`true`，并设置AK信息。关于如何获取AK信息，请参见[获取AccessKey]()。

    ![AK](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2179049061/p179747.png)

5.  在页面右侧，选择目标集群后然后单击**创建**。


## 使用ack-kubernetes-webhook-injector示例

您只需要在Pod的副本控制器中的Pod Spec中使用注解（Annotation）指出Pod需加入的RDS实例ID和RDS白名单分组名称，或安全组的ID。在Pod创建的时候，ack-kubernetes-webhook-injector就会把Pod的IP地址加入到白名单或安全组的规则中，并且在Pod删除时移除规则。

目前已支持的注解有：

-   RDS白名单：
    -   RDS实例ID：ack.aliyun.com/rds\_id
    -   RDS白名单分组名称：ack.aliyun.com/white\_list\_name
-   RDS安全组：ack.aliyun.com/security\_group\_id

本文以自动添加RDS白名单规则为例，说明如何使用ack-kubernetes-webhook-injector为Pod动态添加安全规则。

1.  首先使用以下YAML示例创建一个Deployment，在其中的Pod定义中加入RDS实例ID和RDS白名单分组名称的注解。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: inject-test
      name: inject-test
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: inject-test
      template:
        metadata:
          annotations:
            ack.aliyun.com/rds_id: <rm-wz9nanjcud75bxxxx>
            ack.aliyun.com/white_list_name: <rds_group>
          labels:
            app: inject-test
        spec:
          containers:
          - command:
            - sleep
            - "3600"
            image: alpine:latest
            name: inject-test
    ```

2.  执行以下命令查看Pod的IP地址。

    ```
    kubectl --kubeconfig .kube/config_sts_test -n inject-test get pod -o wide
    ```

    输出：

    ```
    NAME                           READY           STATS        RESTARTS    AGE    IP                NODE
    inject-test-68cc8f9bbf-gj86n    1/1            Running         0        22s   172.25.0.28    cn-hangzhou.xxx
    ```

    可以看出Pod的IP地址是172.25.0.28。

3.  登录[RDS控制台](https://rdsnext.console.aliyun.com/?spm=5176.2020520152.nav-right.2.469016ddzrU6KW#/detail/rm-bp12685y16w4zjz9d/security/whiteList?region=cn-hangzhou)查看注解中RDS实例中的白名单。有关如何查看RDS的白名单，请参见[高安全白名单模式IP白名单](/cn.zh-CN/RDS PostgreSQL 数据库/快速入门/设置白名单/设置白名单.md)。

    可以看到规则已经自动添加。

    ![IP](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0280149061/p179750.png)

4.  将步骤一中的Deployment的副本数设置为0，再检查RDS白名单。

    可以看到刚才创建的规则被自动删除。

    **说明：** 用ack-kubernetes-webhook-injector将Pod IP地址加入安全组的步骤与加入RDS类似。


## 卸载ack-kubernetes-webhook-injector

如果您不再使用ack-kubernetes-webhook-injector，可以在ACK发布功能中将其删除，具体步骤，请参见[删除发布](/cn.zh-CN/Kubernetes集群用户指南/发布/基于Helm的发布管理.md)。同时请执行以下命令清理配置信息。

```
kubectl -n kube-system delete secret kubernetes-webhook-injector-certs
kubectl delete mutatingwebhookconfigurations.admissionregistration.k8s.io kubernetes-webhook-injector
```

