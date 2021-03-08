---
keyword: [云端管控节点, 边缘集群, 添加ECS节点]
---

# 边缘集群云端ECS节点说明

本文主要介绍边缘托管集群中存在的至少一个云服务器ECS（Elastic Compute Service）节点的作用和增、删操作。

## 边缘计算云端管控节点

![边缘管控节点](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7425449951/p100700.png)

在边缘托管集群创建过程中，平台会默认为您创建至少一个ECS实例，并接入到集群管控。该实例主要用来部署云端管控应用，也支持您自定义的云端管控应用部署。通过默认自带**node-role.alibabacloud.com/addon: Effect: NoSchedule**污点（Taints），来保证边缘业务不会部署在云端管控节点。截止1.14.8-aliyunedge.1版本，中心管控节点上默认安装的管控应用有：

-   alibaba-log-controller：日志服务LOG（Log Service，原SLS）控制器。
-   alicloud-monitor-controller：ECS云监控服务控制器。
-   metric-server：集群监控服务端。
-   edge-tunnel-server：反向运维通道服务端，支持通过原生Kubernetes API获取边缘节点、容器监控、SSH远程执行命令等操作。

## 将应用部署到云端管控节点

如果您需要在云端中心管控节点上部署自定义的管控应用，例如各种类型的operator。您需要配置容忍上述提到的污点（Taints）和对应的节点选择器（NodeSelector），配置片段如下。

```
     ...
      nodeSelector:
        alibabacloud.com/is-edge-worker: 'false'
        beta.kubernetes.io/arch: amd64
        beta.kubernetes.io/os: linux
      tolerations:
        - effect: NoSchedule
          key: node-role.alibabacloud.com/addon
          operator: Exists
      ...
```

## 新增云端管控节点

如果您需要在集群中新增云端管控节点，可以按照以下步骤操作（后续支持基于ECS的自动扩缩容能力）。

1.  在集群所在VPC购买ECS。

    购买ECS的步骤，请参见[t9601.md\#]()。

    **说明：** 操作系统选择CentOS 7.6。

2.  在[OpenAPI开发者门户](https://next.api.aliyun.com/api/CS/2015-12-15/OpenAckService?sdkStyle=dara)上使用AttachInstances API，添加ECS至集群。

    body参数如下：

    ```
    {
      "password": "Helloxxxx!",
      "tags":[],
      "instances": [
        "i-uf65mbpn1x8xxxxxx"
      ]
    }
    ```

    |参数|说明|
    |--|--|
    |password|ECS实例密码。密码规则为8~30个字符，且至少同时包含三项（大小写字母、数字和特殊符号）。|
    |tags|给节点打tag标签：    -   key：标签名称。
    -   value：标签值。 |
    |instances|已有实例的数组。|

    更多API信息，请参见[添加已有实例到集群](/cn.zh-CN/API参考/节点/添加已有实例到集群.md)。


**相关文档**  


[边缘托管集群概述](t1884276.md#)

[创建边缘托管版集群](/cn.zh-CN/边缘容器服务ACK@Edge用户指南/边缘托管集群管理/创建边缘托管版集群.md)

