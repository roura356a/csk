---
keyword: [HPA, 常见问题, 诊断]
---

# 弹性伸缩FAQ

本文介绍容器服务ACK弹性伸缩的常见问题及解决办法。

-   [HPA常见问题](#section_7l1_a7b_adu)
-   [节点自动伸缩常见问题](#section_mko_gpj_2h8)
-   [基于阿里云组件指标的容器水平伸缩常见问题](#section_lcs_33o_9tp)
-   其他常见问题
    -   [为何配置自动伸缩组件失败？](~~147427~~)

## HPA常见问题



## 节点自动伸缩常见问题

-   为什么节点自动伸缩组件无法弹出节点？

    请检查是否存在如下几种场景：

    -   配置伸缩组的实例类型无法满足Pod的资源申请（Request）。默认节点会安装系统组件，Pod的申请资源要小于实例的规格。
    -   是否完整按照步骤执行了授权操作。授权操作是集群维度的，需要每个集群操作一次。
-   为什么节点自动伸缩组件无法缩容节点？

    请检查是否存在如下几种场景：

    -   所有的节点Pod的资源申请（Request）阈值高于设置的缩容阈值。
    -   节点上运行kube-system命名空间的Pod。
    -   节点上的Pod包含强制的调度策略，导致其他节点无法运行此Pod。
    -   节点上的Pod拥有[PodDisruptionBudget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/#how-disruption-budgets-work)，且到达了PodDisruptionBudget的最小值。
    您可以在开源社区得到更多关于[节点自动伸缩组件](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md)的常见问题与解答。

-   多个伸缩组在弹性伸缩的时候是如何被选择的？

    在Pod处在无法调度时，会触发弹性伸缩组件的模拟调度逻辑，会根据伸缩组配置的标签和污点以及实例规格等信息进行判断。当配置的伸缩组可以模拟调度Pod的时候，就会被选择进行节点弹出。当同时有多个伸缩组满足模拟调度条件的时候，默认采用的是最少浪费原则，即根据模拟弹出后节点上剩余的资源最小为原则进行抉择。


## 基于阿里云组件指标的容器水平伸缩常见问题

-   问：如果执行`kubectl get hpa`后发现target一栏为<unknow\>怎么办？

    答：请按照以下操作解决。

    1.  执行`kubectl describe hpa <hpa_name>`，确认HPA失效的原因。
        -   如果Conditions字段提示AbleToScale为False，请确认Deployment是否正常部署。
        -   如果Conditions字段提示ScalingActive为False，请继续下一步。
    2.  执行`kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/"`。如果返回为Error from server \(NotFound\): the server could not find the requested resource，请确认alibaba-cloud-metrics-adapter的启动状态。

        如果确认alibaba-cloud-metrics-adapter状态正常，请确认HPA指标是否是Ingress相关指标。如果是Ingress相关指标，您需要提前部署日志服务组件。更多信息，请参见[Ingress访问日志分析与监控](/cn.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress访问日志分析与监控.md)。

    3.  确认HPA指标填写正确。sls.ingress.route的值格式为`<namespace>-<svc>-<port>`。
        -   `<namespace>`为Ingress所在的命名空间。
        -   `<svc>`是Ingress对应的Service名称。
        -   `<port>`是Ingress对应Service的端口名称。
-   问：如何查找HPA支持的指标名称？

    答：请参见[阿里云HPA指标](https://github.com/AliyunContainerService/alibaba-cloud-metrics-adapter)， 以下列举为常用指标。

    |指标名称|描述|附加参数|
    |----|--|----|
    |sls\_ingress\_qps|指定的IngressRoute每秒查询率|sls.ingress.route|
    |sls\_ingress\_latency\_avg|所有请求的延迟|sls.ingress.route|
    |sls\_ingress\_latency\_p50|50%请求的延迟|sls.ingress.route|
    |sls\_ingress\_latency\_p95|95%请求的延迟|sls.ingress.route|
    |sls\_ingress\_latency\_p99|99%请求的延迟|sls.ingress.route|
    |sls\_ingress\_latency\_p9999|99.99%请求的延迟|sls.ingress.route|
    |sls\_ingress\_inflow|Ingress的流入带宽|sls.ingress.route|


**相关文档**  


[metrics-server](/cn.zh-CN/产品发布记录/组件介绍与变更记录/日志与监控/metrics-server.md)

[容器水平伸缩（HPA）](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器水平伸缩（HPA）.md)

[容器定时伸缩（CronHPA）](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器定时伸缩（CronHPA）.md)

[升级安装Kubernetes集群的metrics-server组件](/cn.zh-CN/Kubernetes集群用户指南/集群/升级集群/升级安装Kubernetes集群的metrics-server组件.md)

