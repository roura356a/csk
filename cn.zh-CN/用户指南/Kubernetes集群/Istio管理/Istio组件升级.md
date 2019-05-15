# Istio组件升级 {#concept_k54_vxj_ggb .concept}

本文介绍Istio组件如何升级。

## 背景信息 {#section_sl2_cyj_ggb .section}

-   升级过程可能涉及新的二进制文件以及配置和 API Schemas 等其他更改。
-   升级过程可能导致一些服务宕机 。
-   为了最大限度地减少宕机时间，请使用多副本以保证 Istio 控制平面组件和应用程序具有高可用性。

**说明：** 假设 Istio 组件在 istio-system namespace 中安装和升级。

## 安装包 {#section_kd4_tyj_ggb .section}

阿里云提供了可支持的最新版本的安装文件，包括Helm Chart等。例如：当前提供的版本为1.1.1，对应的下载地址为：

```
https://aliacs-app-catalog.oss-cn-hangzhou.aliyuncs.com/charts/ack-istio-1.1.1.tgz
```

下载该文件后，解压到相应的目录\{ack-istio\}，可以看到如下类似的文件结构：

```
.
├── Chart.yaml
├── LICENSE
├── README.md
├── charts
│   ├── certmanager
│   ├── galley
│   ├── gateways
│   ├── grafana
│   ├── ingress
│   ├── kiali
│   ├── mixer
│   ├── pilot
│   ├── prometheus
│   ├── security
│   ├── servicegraph
│   ├── sidecarInjectorWebhook
│   └── tracing-on-sls
├── requirements.lock
├── requirements.yaml
├── templates
│   ├── _affinity.tpl
│   ├── _helpers.tpl
│   ├── configmap.yaml
│   ├── crds.yaml
│   ├── install-custom-resources.sh.tpl
│   └── sidecar-injector-configmap.yaml
└── values.yaml
```

## 升级步骤 {#section_dxz_1zj_ggb .section}

升级过程可以分为3个部分：CRD升级、控制平面升级、数据平面Sidecar升级。

**CRD升级**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏中的**应用** \> **发布**，进入Helm页面。如果存在**istio-init** 点击删除。
3.  单击左侧导航栏中的**市场** \> **应用目录**，进入应用目录页面。单击**ack-istio-init**，进入应用目录 ack-istio-init页面。
4.  在右侧创建区域，选定**集群**，**空间命名**istio-system，**发布名称**istio-init，单击**创建**，更新Istio的自定义资源CRD。

**控制平面升级**

包括 Citadel、Pilot、Policy、Telemetry及Sidecar injector的升级。

1.  执行以下命令，查看当前部署的 Istio发布的参数配置。

    ```
    helm get values istio > current.values.yaml
    ```

2.  将current.values.yaml的内容合并到ack-istio中的values.yaml中，注意保持values.yaml中的版本为当前新版本的值，例如：

    ```
    global:
      tag: <最新版本>
    ```

3.  升级之前，执行以下命令，进行dry-run 验证：

    ```
    helm upgrade --dry-run --debug istio {ack-istio} --namespace istio-system  -f values.yaml
    ```

4.  在容器服务管理控制台，单击左侧导航栏**应用** \> **发布**，进入发布页面。
5.  选择目标集群，选择目标**发布名称**，单击**操作**列的**更新**，进入更新发布页面。
6.  将更新后的values.yaml内容覆盖到弹出的窗口中，单击**更新**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83226/155788397435271_zh-CN.png)


**数据平面Sidecar升级**

控制平面升级后，已经运行 Istio 的应用程序仍将使用旧版本的 Sidecar。升级Sidecar，则需要重新注入。

**自动注入Sidecar**

如果使用自动注入Sidecar，可以通过对所有 pod 进行滚动升级来升级 Sidecar，这样新版本的 Sidecar 将被自动重新注入。

可以使用如下脚本通过 patch 优雅结束时长来触发滚动更新：

```
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

```
kubectl apply -f <(istioctl kube-inject -f $ORIGINAL_DEPLOYMENT_YAML)
```

如果 Sidecar 以前被注入了一些定制的注入配置文件，请执行以下命令，手动升级 Sidecar：

```
kubectl apply -f <(istioctl kube-inject --injectConfigFile inject-config.yaml --filename $ORIGINAL_DEPLOYMENT_YAML)
```

## 升级中的影响 {#section_b3x_wbk_ggb .section}

**CRD升级**

对于集群内服务间的调用、gateway到服务间的调用均没有产生断连影响。

集群内服务间的调用：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83226/155788397435273_zh-CN.png)

**控制平面升级**

在启用HA（即pilot的replicas为2）的情况下，`istio-pilot/istio-policy/istio-telemetry`的HPA设置： `minReplicas: 2`.

此时，在多次变更版本（升级、回滚）的情况下，测试结果显示为：服务之间的调用QPS基本不变，没有出现断连。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83226/155788397435274_zh-CN.png)

**数据平面Sidecar升级**

无论是集群内服务之间的调用，还是通过gateway到服务的调用，QPS变化不大，但会出现断连情况。建议启动多副本的方式，降低影响。

