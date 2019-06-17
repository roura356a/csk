# Istio组件升级 {#concept_k54_vxj_ggb .concept}

本文介绍Istio组件如何升级。

## 背景信息 {#section_sl2_cyj_ggb .section}

-   升级过程可能涉及新的二进制文件以及配置和 API Schemas 等其他更改。
-   升级过程可能导致一些服务宕机 。
-   为了最大限度地减少宕机时间，请使用多副本以保证 Istio 控制平面组件和应用程序具有高可用性。

**说明：** 假设 Istio 组件在 istio-system namespace 中安装和升级。

## 升级步骤 {#section_dxz_1zj_ggb .section}

升级过程可以分为3个部分：CRD升级及控制平面升级、数据平面Sidecar升级。

**说明：** 升级过程中可能会导致服务不可用，为了最大限度地减少宕机时间，请使用多副本以保证 Istio 控制平面组件和应用程序具有高可用性，包括控制平面和数据平面。

**CRD升级及控制平面升级**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏中的**应用** \> **发布**，进入Helm页面。如果存在**istio-init**并且Chart版本低于1.1.4， 点击**删除**并按照如下步骤安装更新版本。如果存在**istio-init**并且Chart版本不低于1.1.4，点击**更新**，在弹出的窗口下方点击**更新**按钮。
3.  单击左侧导航栏中的**市场** \> **应用目录**，进入应用目录页面。单击**ack-istio-init**，进入应用目录 ack-istio-init页面。
4.  在右侧创建区域，选定**集群**，**空间命名**istio-system，**发布名称**istio-init，单击**创建**，更新Istio的自定义资源CRD及相应的组件。
5.  单击左侧导航栏中的**应用** \> **容器组**，进入容器组页面。单击**istio-init-operator-xxxxxxx-xxxxx**，进入容器组详细页面，点击**日志**页签，根据日志内容是否更新判断该容器组的执行程序是否在执行更新。如果日志内容没有刷新，返回到容器组列表页面，在右侧选择**更多**-\>**删除**，此时会重新拉起该容器组。
6.  执行更新成功后，单击左侧导航栏中的**应用** \> **发布**，进入Helm页面，可以看到Istio组件已经同步更新。

**数据平面Sidecar升级**

控制平面升级后，已经运行 Istio 的应用程序仍将使用旧版本的 Sidecar。升级Sidecar，则需要重新注入。

**自动注入Sidecar**

如果使用自动注入Sidecar，可以通过对所有 pod 进行滚动升级来升级 Sidecar，这样新版本的 Sidecar 将被自动重新注入。

可以使用如下脚本通过 patch 优雅结束时长来触发滚动更新：

``` {#codeblock_o8y_n8a_d2a}
NAMESPACE=$1
DEPLOYMENT_LIST=$(kubectl -n $NAMESPACE get deployment -o jsonpath='{.items[*].metadata.name}')
echo "Refreshing pods in all Deployments: $DEPLOYMENT_LIST"
for deployment_name in $DEPLOYMENT_LIST ; do
    #echo "get TERMINATION_GRACE_PERIOD_SECONDS from deployment: $deployment_name"
    TERMINATION_GRACE_PERIOD_SECONDS=$(kubectl -n $NAMESPACE get deployment "$deployment_name" -o jsonpath='{.spec.template.spec.terminationGracePeriodSeconds}')
    if [ "$TERMINATION_GRACE_PERIOD_SECONDS" -eq 30 ]; then
        TERMINATION_GRACE_PERIOD_SECONDS='31'
    else
        TERMINATION_GRACE_PERIOD_SECONDS='30'
    fi
    patch_string="{\"spec\":{\"template\":{\"spec\":{\"terminationGracePeriodSeconds\":$TERMINATION_GRACE_PERIOD_SECONDS}}}}"
    #echo $patch_string
    kubectl -n $NAMESPACE patch deployment $deployment_name -p $patch_string
done
echo "done."
```

**手动注入Sidecar**

执行以下命令，手动升级 sidecar。

``` {#codeblock_j0u_dt9_0fy}
kubectl apply -f <(istioctl kube-inject -f $ORIGINAL_DEPLOYMENT_YAML)
```

如果 Sidecar 以前被注入了一些定制的注入配置文件，请执行以下命令，手动升级 Sidecar：

``` {#codeblock_lec_u4q_u6s}
kubectl apply -f <(istioctl kube-inject --injectConfigFile inject-config.yaml --filename $ORIGINAL_DEPLOYMENT_YAML)
```

## 升级中的影响 {#section_b3x_wbk_ggb .section}

**CRD升级**

对于集群内服务间的调用、gateway到服务间的调用均没有产生断连影响。

集群内服务间的调用：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83226/156074239035273_zh-CN.png)

**控制平面升级**

在启用HA（即pilot的replicas为2）的情况下，`istio-pilot/istio-policy/istio-telemetry`的HPA设置： `minReplicas: 2`.

此时，在多次变更版本（升级、回滚）的情况下，测试结果显示为：服务之间的调用QPS基本不变，没有出现断连。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83226/156074239035274_zh-CN.png)

**数据平面Sidecar升级**

无论是集群内服务之间的调用，还是通过gateway到服务的调用，QPS变化不大，但会出现断连情况。建议启动多副本的方式，降低影响。

