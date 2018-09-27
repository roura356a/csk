# 基于Istio实现多Kubernetes集群上的应用服务混合编排 {#concept_nsk_d25_cfb .concept}

Istio 一个重要目标是支持混合型环境，例如您可以将应用运行在阿里云容器服务、本地Kubernetes 集群，或是其他公有云的服务上。Istio 能够为整体服务平台提供一个统一的视图，管理这些环境之间的连接并确保安全性。Istio多集群支持的架构，允许将多个 Kubernetes 集群加入一个单独的服务网格（Service Mesh）中，并启用跨集群的服务发现功能。按照官方介绍，它还将支持全球化的集群级别的负载均衡，并通过 Gateway 对等互联提供对非扁平网络的支持。

在[基于Istio实现Kubernetes与ECS上的应用服务混合编排](intl.zh-CN/最佳实践/基于Istio实现Kubernetes与ECS上的应用服务混合编排.md#)中介绍阿里云Kubernetes容器服务提供的Istio Mesh Expansion整合能力。

本文通过一个官方示例来重点介绍在阿里云容器服务上如何基于Istio实现多Kubernetes集群上的应用服务混合编排。你可以看到在一个 Kubernetes 集群上安装 Istio 控制平面，然后Istio连接了2个 Kubernetes 集群之后，就会生成一个跨越多个 Kubernetes 集群的网格网络。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21317/153804280912672_zh-CN.png)

## 准备Kubernetes集群 {#section_evd_f25_cfb .section}

阿里云容器服务Kubernetes 1.10.4目前已经上线，可以通过容器服务管理控制台非常方便地快速创建 Kubernetes 集群。具体过程可以参考[创建Kubernetes集群](../../../../intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。

确保安装配置kubectl 能够连接上Kubernetes 集群，并满足以下网络要求：

-   各集群的 Pod CIDR 范围和 Service CIDR 范围必须是唯一的，不允许相互重叠。
-   每个集群中的所有的 Pod CIDR 需要能够互相路由。
-   所有的 Kubernetes 控制平面 API Server 互相可路由。

|集群|ContainerCIDR|ServiceCIDR|
|--|-------------|-----------|
|Istio-control-plane|172.16.0.0/16|172.19.0.0/20|
|Istio-remote1|172.20.0.0/16|172.21.0.0/20|

本示例中涉及到的文件可以从[示例](https://github.com/osswangxining/istio-multicluster) 获取。

## 安装Istio控制平面 {#section_ivd_f25_cfb .section}

接着，就像前面系列文章中介绍的步骤，通过应用目录简便部署Istio。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧的**市场** \> **应用目录**，在右侧选中`ack-istio`。
3.  在打开的页面中选择命名空间**istio-system**，并单击**参数**, 可以通过修改参数配置进行定制化安装：

    ```
    ......
       istio-ilbgateway:
          enabled: true
    ......
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21317/153804280912673_zh-CN.png)

    **说明：** 说明文档提供了安装和卸载的一些重要信息，特别是常见的CRD（custom resource definition）版本问题。

    **说明：** 在使用阿里云Kubernetes容器服务Istio 1.0的过程中，如果遇到类似CRD版本问题，请参考我们提供的[阿里云Kubernetes容器服务Istio实践之常见问题分析](https://yq.aliyun.com/articles/621026)。 我们会持续更新遇到的问题及其解决方法。


## 安装 Istio 远程组件 {#section_lvd_f25_cfb .section}

通过以下脚本可以获取到控制平面的连接信息：

```
export PILOT_POD_IP=$(kubectl -n istio-system get pod -l istio=pilot -o jsonpath='{.items[0].status.podIP}')
export POLICY_POD_IP=$(kubectl -n istio-system get pod -l istio=mixer -o jsonpath='{.items[0].status.podIP}')
export STATSD_POD_IP=$(kubectl -n istio-system get pod -l istio=statsd-prom-bridge -o jsonpath='{.items[0].status.podIP}')
export TELEMETRY_POD_IP=$(kubectl -n istio-system get pod -l istio-mixer-type=telemetry -o jsonpath='{.items[0].status.podIP}')
export ZIPKIN_POD_IP=$(kubectl -n istio-system get pod -l app=jaeger -o jsonpath='{.items[0].status.podI
P}')
echo "remotePilotAddress: $PILOT_POD_IP"
echo "remotePolicyAddress: $POLICY_POD_IP"
echo "remoteStatsdPromBridge: $STATSD_POD_IP"
echo "remoteTelemetryAddress: $TELEMETRY_POD_IP"
echo "remoteZipkinAddress: $ZIPKIN_POD_IP"
```

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧的**市场** \> **应用目录**，在右侧选中`ack-istio-remote`。
3.  在打开的页面中选择命名空间**istio-system**，并单击**参数**，将这些信息填充到参数配置中进行定制化安装：

    ```
      .....
      envoyStatsd:
        enabled: false
        host: "$remoteStatsdPromBridge"
    ```

    ```
    # Remote Istio endpoints. Can be hostnames or IP addresses# The Pilot address is required. The others are optional.
    remotePilotAddress: "$remotePilotAddress"
    remotePolicyAddress: "$remotePolicyAddress"
    remoteTelemetryAddress: "$remoteTelemetryAddress"
    remoteZipkinAddress: "$remoteZipkinAddress"
    ```


## 安装示例到集群Istio-control-plane {#section_mvd_f25_cfb .section}

首先通过如下命令行或者控制台创建命名空间 `bookinfo`, 并部署我们修改之后的应用。在这个修改后的版本中去掉了`details`组件，并定义了`ingressgateway` 。

本示例中涉及到的文件可以从 [这里](https://github.com/osswangxining/istio-multicluster) 获取到。

```
kubectl create ns bookinfo 
kubectl label namespace bookinfo istio-injection=enabled
kubectl apply -n bookinfo -f ./bookinfo/bookinfo-without-details.yaml
kubectl apply -n bookinfo -f ./bookinfo/bookinfo-gateway.yaml
```

基于官方示例修改的部署，其中除了`details`组件运行在安装Istio本地控制平面的Kubernetes集群Istio-control-plane中，而`details`组件运行在另外一个Kubernetes集群Istio-remote-1中：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21317/153804280912674_zh-CN.png)

正常运行之后，通过`ingressgateway`暴露的地址访问`/productpage`页面，效果应该如下所示，`details`部分应该不能正常显示（因为此时`details`组件还没有安装到集群Istio-remote-1并加入到同一网格中）：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21317/153804280912675_zh-CN.png)

## 远程集群配置 {#section_pvd_f25_cfb .section}

Istio 控制平面需要访问网格中的所有集群，来完成服务发现的目的。下面描述了如何在远程集群中创建一个 Service account，并赋予它必要的 RBAC 权限；后面还会使用这个 Service account 的凭据为远程集群生成一个 kubeconfig 文件，这样就可以访问远程集群了。

下面的过程应该在每一个要加入到服务网格中的集群上执行。这个过程需要对应集群的管理员用户来完成。运行命令`generate-kubeconfig.sh`，参数为描述所在集群的唯一标示：

```
 ./generate-kubeconfig.sh myremote1
```

完成这些步骤之后，就在当前目录中创建了远程集群的 kubeconfig 文件。集群的文件名和原始的 kubeconfig 集群名称一致。

## 控制平面集群配置 {#section_qvd_f25_cfb .section}

在运行 Istio 控制平面的集群上为每个远程集群创建一个 Secret：

```
 ./create-secret.sh myremote1
```

## 安装示例到集群Istio-remote-1 {#section_rvd_f25_cfb .section}

同样的步骤，安装示例中`details`部分到集群Istio-remote-1，YAML文件可以从 [示例文件](https://github.com/osswangxining/istio-multicluster) 获取到。

```
kubectl apply -n bookinfo -f ./bookinfo/bookinfo-details.yaml
```

之后，`details`服务将会被注册到控制平面中，可以查看`{pilot-ipAddress}:8080/v1/registration`返回结果是否包含 `details`服务：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21317/153804280912676_zh-CN.png)

再次访问`/productpage`页面，效果应该如下所示，`details`部分应该可以正常显示：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21317/153804281012677_zh-CN.png)

## 总结 {#section_uvd_f25_cfb .section}

本文通过一个官方示例，来重点介绍在阿里云容器服务上如何基于Istio实现多Kubernetes集群上的应用服务混合编排。你可以看到在 一个 Kubernetes 集群上安装 Istio 控制平面，然后Istio连接了2个 Kubernetes 集群之后，就会生成一个跨越多个 Kubernetes 集群的网格网络。

欢迎大家使用阿里云上的容器服务，快速搭建微服务的开放治理平台Istio，比较简单地集成到自己项目的微服务开发中。

