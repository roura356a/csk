# 使用钉钉实现Kubernetes集群监控告警 {#task_qts_14g_hfb .task}

在钉钉群部署群机器人后，当Kubernetes集群出现异常事件时，可将异常事件通过群机器人发送到指定钉钉群，从而实现集群异常事件的实时监控告警。

-   您已成功创建一个钉钉群。
-   您已成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。

1.  单击钉钉群右上角![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154019318013518_zh-CN.png)图标，进入群设置页面。 
2.   单击**群机器人**，进入群机器人页面，选择需要添加的机器人。此处选择**自定义**机器人。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154019318013519_zh-CN.png)

 
3.   在机器人详情页面，单击**添加**，进入添加机器人页面。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154019318013520_zh-CN.png)

 
4.  根据如下信息配置群机器人后，单击**完成添加**： 

    |配置|说明|
    |--|--|
    |编辑头像|（可选）为群机器人设置头像。|
    |机器人名字|添加的机器人名称。|
    |添加到群组|添加机器人的群组。|
    |是否开启Outgoing机制|（可选）通过@群机器人，将消息发送到指定外部服务，还可以将外部服务的响应结果返回到群组。**说明：** 建议不开启。

|
    |POST 地址|接收消息的HTTP服务地址。**说明：** 当选择开启Outgoing机制时，此项可配置。

|
    |Token|用于验证请求来自钉钉的密钥。**说明：** 当选择开启Outgoing机制时，此项可配置。

|

5.   单击**复制**，复制webhook地址。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154019318013532_zh-CN.png)

 

    **说明：** 在群机器人页面，选择目标群机器人，单击右侧![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154019318013551_zh-CN.png)图标可以：

    -   修改群机器人的头像及机器人名字。
    -   **开启**或**关闭**消息推送。
    -   重置webhook地址。
    -   删除群机器人。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154019318013550_zh-CN.png)

6.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。 
7.  在Kubernetes菜单下，单击左侧导航栏中的**应用** \> **部署**，进入 部署列表页面。 
8.   选择目标集群，命名空间选为**kube-system**，单击右上角**使用模板创建**。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154019318013533_zh-CN.png)

 
9.  根据以下信息配置模板，完成后单击**创建**。 

    |配置|说明|
    |--|--|
    |集群|选择目标集群。|
    |命名空间|选择资源对象所属的命名空间，默认是 **default**。此处选择**kube-system**。|
    |示例模板|阿里云容器服务提供了多种资源类型的 Kubernetes yaml 示例模板，让您快速部署资源对象。您可以根据 Kubernetes Yaml 编排的格式要求自主编写，来描述您想定义的资源类型。此处选择**自定义**。|
    |模板|填写以下自定义内容：    ```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: eventer
  namespace: kube-system
spec:
  replicas: 1
  template:
    metadata:
      labels:
        task: monitoring
        k8s-app: eventer
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ''
    spec:
      serviceAccount: admin
      containers:
      - name: eventer
        image: registry.cn-hangzhou.aliyuncs.com/acs/eventer:v1.6.0
        imagePullPolicy: IfNotPresent
        command:
        - /eventer
        - --source=kubernetes:https://kubernetes.default
        - --sink=dingtalk:[your_webhook_url]&label=[your_cluster_id]&level       #level可配置为：Normal或Warning，默认值为：Warning。当配置Normal时，会在钉钉群收到Normal和Warning级别的告警；不配置或配置为Warning时，钉钉群仅收到Warning级别的告警。

    ```

|

    在集群列表页面选择目标集群，单击操作列**控制台**，进入Kubernetes 控制台，选择**命名空间**为**kube-system**，单击左侧导航栏**部署**，可查看到eventer已部署成功。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154019318013541_zh-CN.png)


部署成功后30s，eventer生效，当事件等级超过阈值等级时，即可在钉钉群收到如下告警：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154019318013547_zh-CN.png)

