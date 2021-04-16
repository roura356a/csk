---
keyword: [创建NPU集群, NPU资源]
---

# 创建NPU集群

您可以通过容器服务管理控制台非常方便的创建带有NPU资源的Kubernetes集群。

创建集群过程中，容器服务会进行如下操作：

-   创建ECS，配置管理节点到其他节点的SSH的公钥登录，通过CloudInit安装配置Kubernetes集群。
-   创建安全组，该安全组允许VPC入方向全部ICMP端口的访问。
-   如果您不使用已有的VPC网络，会为您创建一个新的VPC及VSwitch，同时为该VSwitch创建SNAT。
-   创建VPC路由规则。
-   创建 NAT网关及EIP。
-   创建RAM子账号和AK，该子账号拥有ECS的查询、实例创建和删除的权限、添加和删除云盘的权限、SLB的全部权限、云监控的全部权限、VPC的全部权限、日志服务的全部权限、NAS的全部权限。Kubernetes集群会根据用户部署的配置相应的动态创建SLB、云盘、VPC路由规则。
-   创建内网SLB，暴露6443端口。
-   创建公网SLB，暴露6443、8443和22 端口（如果您在创建集群的时候选择开放公网SSH登录，则会暴露22端口；如果您选择不开放公网SSH 访问，则不会暴露22端口）。

## 使用限制

-   随集群一同创建的负载均衡实例只支持按量付费的方式。
-   Kubernetes集群仅支持专有网络VPC。
-   每个账号默认可以创建的云资源有一定的配额，如果超过配额创建集群会失败。请在创建集群前确认您的配额。

    如果您需要提高配额，请提交工单申请。

    -   每个账号默认最多可以创建50个集群（所有地域下），每个集群中最多可以添加100个节点。如果您需要创建更多的集群或者节点，请[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请。

        **说明：** 每个账户初始默认状况下VPC路由条目不超过48条，意味着您的Kubernetes集群的网络模式是Flannel时，集群的节点数最大不能超过48个（网络模式是Terway则不受该影响）。如集群需要更多节点数，您需要先对目标VPC[提交工单](https://workorder-intl.console.aliyun.com/console.htm)，申请提高配额 。

    -   每个账号默认最多可以创建100个安全组。
    -   每个账号默认最多可以创建60个按量付费的负载均衡实例。
    -   每个账号默认最多可以创建20个EIP。
-   ECS实例使用限制：

    支持创建按量付费和包年包月的ECS实例。

    **说明：** 实例创建后，您可以通过ECS管理控制台将按量付费转预付费，请参见[按量付费转包年包月](/intl.zh-CN/产品计费/转换计费方式/按量付费转包年包月.md)。


## 创建NPU型Kubernetes集群

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击页面右上角的**集群模板**。

4.  在选择集群模板页面，选择异构计算专有集群页面，并单击**创建**。

    本例中创建的是异构计算专有集群，您也可以在选择集群模板页面选择**异构计算托管集群**并进行以下操作。

    **说明：** 为了创建NPU集群，通常情况下，Worker节点使用NPU类型的ECS。集群其他参数配置，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

5.  设置Worker节点的配置信息。本例中将Worker节点作为NPU工作节点，选择NPU计算型实例规格ecs.ebman1.26xlarge。

    -   若您选择新增实例，则需要选择Worker节点的系列和规格，以及需要创建的Worker节点的数量（本示例创建2个NPU节点，实例类型为ecs.ebman1.26xlarge）。
    -   若您选择添加已有实例，则需要预先在此地域下创建NPU云服务器。请参见[实例规格族](/intl.zh-CN/实例/实例规格族.md)。
6.  完成其他配置后，单击**创建集群**，启动部署。

7.  查看节点挂载的NPU设备。

    1.  在控制台左侧导航栏中，单击**集群**。

    2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**应用管理**。

    3.  在集群管理页面单击左侧导航栏选择**节点管理** \> **节点**。

    4.  选择创建集群时配置的Worker节点，单击**操作**列的**更多** \> **详情**，查看该节点挂载的NPU设备。


## 配置私有镜像密钥

如果您需要使用阿里云提供的NPU类型的Docker镜像，可以联系与您对接的客户经理或销售人员，获取一个已授权的账号。下载Docker镜像并在Kubernetes集群中配置私有镜像密钥。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面单击目标集群右侧**操作**列的**更多** \> **通过CloudShell管理集群**。

    集群连接成功后，界面显示如下。

    ![显示结果](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7875659951/p70024.png)

4.  执行以下命令，创建一个docker-registry类型的Secret。

    ```
    kubectl create secret \
    docker-registry regsecret \
    --docker-server=registry.cn-shanghai.aliyuncs.com \
    --docker-username=<your_username>
    --docker-password=<your_password>
    ```

    **说明：**

    -   `regsecret`：指定密钥的键名称，可自行定义。
    -   `--docker-server`：指Docker仓库地址。
    -   `--docker-username`：获取的账号。
    -   `--docker-password`：获取的密码。
5.  在Pod的配置文件中添加Secret，拉取NPU类型的私有镜像。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: test-npu
    spec:
      containers:
      - name: <容器名称>
        image: registry.cn-shanghai.aliyuncs.com/hgai/<NPU类型的docker镜像>
      imagePullSecrets:
      - name: <secret名称>
    ```

    **说明：**

    -   `imagePullSecrets`是声明拉取镜像时需要指定密钥。
    -   `regsecret`必须和步骤[3](#step_cri_jrl_v1r)生成密钥的键名一致。
    -   `image`中的Docker仓库名称必须和`--docker-server`中的Docker仓库名一致。

