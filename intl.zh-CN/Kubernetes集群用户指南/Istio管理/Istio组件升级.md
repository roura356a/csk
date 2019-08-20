# Istio组件升级 {#task_1797295 .task}

本文介绍Istio组件如何升级。

-   升级过程可能涉及新的二进制文件以及配置和 API Schemas 等其他更改。
-   升级过程可能导致一些服务宕机 。
-   为了最大限度地减少宕机时间，请使用多副本以保证 Istio 控制平面组件和应用程序具有高可用性。
-   升级过程中可能会因为配置参数的变化对服务的SLB重新创建，建议通过设置SLB删除保护来保留已有SLB。

**说明：** 假设 Istio 组件在 istio-system namespace 中安装和升级。

升级过程可以分为3个部分：CRD升级、控制平面升级、数据平面Sidecar升级。

**说明：** 升级过程中可能会导致服务不可用，为了最大限度地减少宕机时间，请使用多副本以保证 Istio 控制平面组件和应用程序具有高可用性，包括控制平面和数据平面。

## CRD升级及控制平面升级 {#section_x6q_4tg_dam .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏中的**服务网格** \> **Istio管理**，进入Istio管理页面。如果版本低于1.1.4， 请工单联系进行升级。如果版本不低于1.1.4，并且提示升级，则单击**升级**链接进行自动升级。注意：升级过程会对之前做过的针对Kubernetes资源\(如服务、部署等\)的直接修改进行重置，因此建议所有的修改都通过Istio更新操作以保证所做的修改不会被重置。

## 数据平面Sidecar升级 {#section_ob3_8ry_q98 .section}

控制平面升级后，已经运行 Istio 的应用程序仍将使用旧版本的 Sidecar。升级Sidecar，则需要重新注入。您可以选择以下两种操作升级Sidecar。

-   **自动注入Sidecar** 

    如果使用自动注入Sidecar，可以通过对所有 pod 进行滚动升级来升级 Sidecar，这样新版本的 Sidecar 将被自动重新注入。

    可以使用如下脚本通过 patch 优雅结束时长来触发滚动更新：

    ``` {#codeblock_mxl_3w0_oc8}
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

-   **手动注入Sidecar** 

    执行以下命令，手动升级 sidecar。

    ``` {#codeblock_flt_kij_7zo}
    kubectl apply -f <(istioctl kube-inject -f $ORIGINAL_DEPLOYMENT_YAML)
    ```

    如果 Sidecar 以前被注入了一些定制的注入配置文件，请执行以下命令，手动升级 Sidecar：

    ``` {#codeblock_7uj_yk7_yyp}
    kubectl apply -f <(istioctl kube-inject --injectConfigFile inject-config.yaml --filename $ORIGINAL_DEPLOYMENT_YAML)
    ```


## 升级中的影响 {#section_lpv_p3h_t1l .section}

-   **CRD升级** 

    对于集群内服务间的调用、gateway到服务间的调用均没有产生断连影响。

    集群内服务间的调用：

    ![CRD升级](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83226/156629558435273_zh-CN.png)

-   **控制平面升级** 

    在启用HA（即pilot的replicas为2）的情况下，`istio-pilot/istio-policy/istio-telemetry`的HPA设置： `minReplicas: 2`。

    此时，在多次变更版本（升级、回滚）的情况下，测试结果显示为：服务之间的调用QPS基本不变，没有出现断连。

    ![控制平面升级](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83226/156629558435274_zh-CN.png)

-   **数据平面Sidecar升级** 

    无论是集群内服务之间的调用，还是通过gateway到服务的调用，QPS变化不大，但会出现断连情况。建议启动多副本的方式，降低影响。


