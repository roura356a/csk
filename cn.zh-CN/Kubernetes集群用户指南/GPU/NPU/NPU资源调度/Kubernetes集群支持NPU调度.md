---
keyword: [NPU调度, NPU资源]
---

# Kubernetes集群支持NPU调度

本文主要为您介绍如何创建和使用带有NPU资源的Kubernetes集群。

您需要开通容器服务和访问控制（RAM）服务。

与大型算法模型中CPU相比，NPU处理器最明显的优势是信息处理能力快。NPU采用了“数据驱动并行计算”架构，颠覆了CPU所采用的传统冯·诺依曼计算机架构，这种数据流类型的处理器大大提升了计算能力与功耗的比率。NPU特别适合处理视频、图像类的海量多媒体数据的场景，不仅速度比CPU要高出100 ~ 1000倍，同时功耗也远远低于CPU。

您可以通过阿里云Kubernetes集群 + ALI NPU运行机器学习，图像处理等高运算密度等任务，实现快速部署和弹性扩缩容等功能。

**说明：** 如果想了解ALI NPU的相关信息，请参见[AliNPU Website](https://www.t-head.cn/)。

本文将以创建Kubernetes集群时，添加ecs.ebman1.26xlarge实例为例说明如何使用NPU。

创建集群过程中，容器服务会进行以下操作：

-   创建ECS，配置管理节点到其他节点的SSH的公钥登录，通过CloudInit安装配置Kubernetes集群。
-   创建安全组，该安全组允许VPC入方向全部ICMP端口的访问。
-   如果您不使用已有的VPC网络，会为您创建一个新的VPC及VSwitch，同时为该VSwitch创建SNAT。
-   创建VPC路由规则。
-   创建NAT网关及EIP。
-   创建RAM子账号和AccessKey，该子账号拥有ECS的查询、实例创建和删除的权限，添加和删除云盘的权限，SLB的全部权限，云监控的全部权限，VPC的全部权限，日志服务的全部权限，NAS的全部权限。Kubernetes集群会根据用户部署的配置相应的动态创建SLB、云盘、VPC路由规则。
-   创建内网SLB，暴露6443端口。
-   创建公网SLB，暴露6443、8443和22端口（如果您在创建集群的时候选择开放公网SSH登录，则会暴露22端口；如果您选择不开放公网SSH访问，则不会暴露22端口）。

## 使用限制

-   用户账户需有100元的余额并通过实名认证，否则无法创建按量付费的ECS实例和负载均衡。
-   随集群一同创建的负载均衡实例只支持按量付费的方式。
-   Kubernetes集群仅支持专有网络VPC。
-   每个账号默认可以创建的云资源有一定的配额，如果超过配额创建集群会失败。请在创建集群前确认您的配额。

    如果您需要提高配额，请提交工单申请。

    -   每个账号默认最多可以创建50个集群（所有地域下），每个集群中最多可以添加100个节点。如果您需要创建更多的集群或者节点，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)申请。

        **说明：** 每个账户初始默认状况下VPC路由条目不超过48条，意味着您的Kubernetes集群的网络模式是Flannel时，集群的节点数最大不能超过48个（网络模式是Terway则不受该影响）。如集群需要更多节点数，您需要先对目标VPC[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)，申请提高配额 。

    -   每个账号默认最多可以创建100个安全组。
    -   每个账号默认最多可以创建60个按量付费的负载均衡实例。
    -   每个账号默认最多可以创建20个EIP。
-   ECS实例使用限制：

    支持创建按量付费和包年包月的ECS实例。

    **说明：** 实例创建后，您可以通过ECS管理控制台将按量付费转预付费，请参见[按量付费转包年包月](/cn.zh-CN/产品计费/转换计费方式/按量付费转包年包月.md)。


## 创建NPU型Kubernetes集群

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击页面右上角的**集群模板**。

4.  在选择集群模板页面，选择异构计算专有集群页面，并单击**创建**。

    本例中创建的是异构计算专有集群，您也可以在选择集群模板页面选择**异构计算托管集群**并进行以下操作。

    **说明：** 为了创建NPU集群，通常情况下，Worker节点使用NPU类型的ECS。集群其他参数配置，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

5.  设置Worker节点的配置信息。本例中将Worker节点作为NPU工作节点，选择NPU计算型实例规格ecs.ebman1.26xlarge。

    -   若您选择新增实例，则需要选择Worker节点的系列和规格，以及需要创建的Worker节点的数量（本示例创建2个NPU节点，实例类型为ecs.ebman1.26xlarge）。
    -   若您选择添加已有实例，则需要预先在此地域下创建NPU云服务器。请参见[实例规格族](/cn.zh-CN/实例/实例规格族.md)。
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

## 使用NPU资源

如果某一个Pod需要使用NPU资源，需要在`resources.limits`定义`aliyun.com/npu`的值。

```
apiVersion: v1
kind: Pod
metadata:
  name: <pod名称>
spec:
  containers:
    - name: <容器名称>
      image: <镜像名称>
      resources:
        limits:
          aliyun.com/npu: <请求npu资源数>
```

## 运行TensorFLow的NPU实验环境

您可以在集群中使用NPU资源完成模型训练。本例中，将会启动一个使用NPU资源的Pod进行模型训练。

1.  连接集群，请参见[在CloudShell上通过kubectl管理Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/在CloudShell上通过kubectl管理Kubernetes集群.md)。

    在CloudShell界面执行以下操作。

    ```
    cat > test-pod.yaml <<- EOF
    apiVersion: v1
    kind: Pod
    metadata:
      name: test-npu-pod
    spec:
      restartPolicy: Never
      imagePullSecrets:
        - name: regsecret
      containers:
        - name: resnet50-npu
          image: registry.cn-shanghai.aliyuncs.com/hgai/tensorflow:v1_resnet50-tensorflow1.9.0-toolchain1.0.2-centos7.6
          resources:
            limits:
              aliyun.com/npu: 1 # requesting  NPUs
    EOF
    ```

2.  执行以下命令，创建Pod。

    ```
    kubectl apply -f test-pod.yaml
    ```

3.  执行以下命令，查看Pod的状态。

    ```
    kubectl get po test-npu-pod
    ```

    **说明：** 如果Pod为Error状态，请执行`kubectl logs test-npu-pod`命令监听Pod日志，并排查修改。


等待一段时间后，您可以通过执行以下命令查看Pod的状态。

```
kubectl get po test-npu-pod
```

如果查看到Pod的状态为Completed，再执行以下命令查看日志。

```
kubectl logs test-npu-pod
```

此时看到日志显示结果如下，表示训练任务完成。

```
2019-10-30 12:10:50.389452: I tensorflow/core/platform/cpu_feature_guard.cc:141] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX2 AVX512F FMA
100%|##########| 98/98 [00:26<00:00,  3.67it/s]
resnet_v1_50, result =  {'top_5': 0.9244321584701538, 'top_1': 0.7480267286300659}
```

