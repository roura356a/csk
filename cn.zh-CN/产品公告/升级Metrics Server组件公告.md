---
keyword: [Metrics Server, 组件, 升级]
---

# 升级Metrics Server组件公告

Metrics-Server组件底层依赖的网络http2库在网络抖动的场景下，偶发出现`http2: no cached connection was available`的报错，可能造成`kubectl top node/pod`无返回结果、HPA无法生效、删除namespace阻塞等现象。

## 影响范围

受此影响的版本可以通过升级组件的方式修复，v0.2.3-8a48069-aliyun及以上版本默认已修复，无需升级。

-   影响版本：v0.2.2-473c961-aliyun、v0.2.2-b4bf266-aliyun
-   修复版本：v0.2.3-8a48069-aliyun及以上

## 升级组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面单击目标集群**操作**列下的**更多**，选择**系统组件管理**。

4.  在**组件管理**页面，单击**日志与监控**页签，找到Metrics-Server组件卡片，单击**升级**，在**提示**对话框中单击**确定**。

    升级成功后，组件卡片会显示**升级成功**。


