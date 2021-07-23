---
keyword: [HPA, 常见问题, 诊断]
---

# 弹性伸缩FAQ

本文介绍容器服务ACK弹性伸缩的常见问题及解决办法。

|类型|问题|
|--|--|
|水平伸缩（HPA）常见问题|-   [HPA的监控数据current字段为何显示为unknown？](#section_7l1_a7b_adu)
-   [为何HPA在滚动发布时出现扩容多余Pod？](#section_oxl_0ov_og8)
-   [HPA到达阈值为何不进行扩缩容？](#section_1tg_5jx_p4h)
-   [HPA采集周期如何配置？](#section_i3r_lgt_w1n)
-   [CronHPA是否兼容HPA？](#section_ilk_54c_4f6)
-   [如何解决HPA启动时CPU或内存飙高造成扩容出多余Pod的多弹现象？](#section_2cl_008_8x0) |
|基于组件指标的水平伸缩常见问题|-   [如果执行kubectl get hpa后发现target一栏为<unknow\>怎么办？](#li_k72_y2i_lz3)
-   [如何查找HPA支持的指标名称？](#li_e6q_0mz_ifx) |
|节点自动伸缩常见问题|-   [为什么节点自动伸缩组件无法弹出节点？](#li_i42_qfu_zso)
-   [为什么节点自动伸缩组件无法缩容节点？](#li_e4c_m7x_79f)
-   [多个伸缩组在弹性伸缩的时候是如何被选择的？](#li_kuk_jgp_l95) |

## HPA的监控数据current字段为何显示为unknown？

当HPA的监控数据的current字段显示为unknown时，表示kube-controller-manager无法访问监控数据源获取对应的监控数据。

```
Name:                                                  kubernetes-tutorial-deployment
Namespace:                                             default
Labels:                                                <none>
Annotations:                                           <none>
CreationTimestamp:                                     Mon, 10 Jun 2019 11:46:48  0530
Reference:                                             Deployment/kubernetes-tutorial-deployment
Metrics:                                               ( current / target )
  resource cpu on pods  (as a percentage of request):  <unknown> / 2%
Min replicas:                                          1
Max replicas:                                          4
Deployment pods:                                       1 current / 0 desired
Conditions:
  Type           Status  Reason                   Message
  ----           ------  ------                   -------
  AbleToScale    True    SucceededGetScale        the HPA controller was able to get the target's current scale
  ScalingActive  False   FailedGetResourceMetric  the HPA was unable to compute the replica count: unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server is currently unable to handle the request (get pods.metrics.k8s.io)
Events:
  Type     Reason                   Age                      From                       Message
  ----     ------                   ----                     ----                       -------
  Warning  FailedGetResourceMetric  3m3s (x1009 over 4h18m)  horizontal-pod-autoscaler  unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server is currently unable to handle the request (get pods.metrics.k8s.io)
```

原因如下：

-   原因一：resource metrics数据源无法使用。先执行命令`kubectl top pod`检查是否返回数据。如果所有的Pod都无数据，请执行`kubectl get apiservice`检查当前提供resource metrics的数据源的情况。返回的示例数据如下。

    ```
    NAME                                   SERVICE                      AVAILABLE   AGE
    v1.                                    Local                        True        29h
    v1.admissionregistration.k8s.io        Local                        True        29h
    v1.apiextensions.k8s.io                Local                        True        29h
    v1.apps                                Local                        True        29h
    v1.authentication.k8s.io               Local                        True        29h
    v1.authorization.k8s.io                Local                        True        29h
    v1.autoscaling                         Local                        True        29h
    v1.batch                               Local                        True        29h
    v1.coordination.k8s.io                 Local                        True        29h
    v1.monitoring.coreos.com               Local                        True        29h
    v1.networking.k8s.io                   Local                        True        29h
    v1.rbac.authorization.k8s.io           Local                        True        29h
    v1.scheduling.k8s.io                   Local                        True        29h
    v1.storage.k8s.io                      Local                        True        29h
    v1alpha1.argoproj.io                   Local                        True        29h
    v1alpha1.fedlearner.k8s.io             Local                        True        5h11m
    v1beta1.admissionregistration.k8s.io   Local                        True        29h
    v1beta1.alicloud.com                   Local                        True        29h
    v1beta1.apiextensions.k8s.io           Local                        True        29h
    v1beta1.apps                           Local                        True        29h
    v1beta1.authentication.k8s.io          Local                        True        29h
    v1beta1.authorization.k8s.io           Local                        True        29h
    v1beta1.batch                          Local                        True        29h
    v1beta1.certificates.k8s.io            Local                        True        29h
    v1beta1.coordination.k8s.io            Local                        True        29h
    v1beta1.events.k8s.io                  Local                        True        29h
    v1beta1.extensions                     Local                        True        29h
    ...
    [v1beta1.metrics.k8s.io                 kube-system/metrics-server   True        29h]
    ...
    v1beta1.networking.k8s.io              Local                        True        29h
    v1beta1.node.k8s.io                    Local                        True        29h
    v1beta1.policy                         Local                        True        29h
    v1beta1.rbac.authorization.k8s.io      Local                        True        29h
    v1beta1.scheduling.k8s.io              Local                        True        29h
    v1beta1.storage.k8s.io                 Local                        True        29h
    v1beta2.apps                           Local                        True        29h
    v2beta1.autoscaling                    Local                        True        29h
    v2beta2.autoscaling                    Local                        True        29h
    ```

    如果v1beta1.metrics.k8s.io所对应的apiservice不是kube-system/metrics-server，检查是否由于安装Prometheus Operator覆盖导致。如果是覆盖导致的问题，可以通过部署以下的YAML模板进行恢复。

    ```
    apiVersion: apiregistration.k8s.io/v1beta1
    kind: APIService
    metadata:
      name: v1beta1.metrics.k8s.io
    spec:
      service:
        name: metrics-server
        namespace: kube-system
      group: metrics.k8s.io
      version: v1beta1
      insecureSkipTLSVerify: true
      groupPriorityMinimum: 100
      versionPriority: 100
    ```

    如果非上述问题，请参见[问题诊断](/intl.zh-CN/产品发布记录/组件介绍与变更记录/日志与监控/metrics-server.md)。

-   原因二：滚动发布或者扩容时无法获取数据。

    默认metrics-server的采集周期是1秒。刚扩容或更新完成后，metric-server会有一段时间无法获取监控数据。请于扩容或更新后2秒左右进行查看。

-   原因三：缺少request字段设置。

    HPA默认是通过实际的利用率/request作为利用率的数值，因此可以检查Pod的Resource字段中是否包含Request字段。


## 为何HPA在滚动发布时出现扩容多余Pod？

社区的Controller Manager会在滚动发布的时候，对于没有监控数据的Pod，进行监控数据的补零操作，从而有一定的概率出现扩容出多余的Pod现象（多弹现象）。您可以通过升级ACK提供的最新版metrics-server，并在metrics-server的启动参数上开启开关防止多弹。

```
##在metrics-server的启动参数中加入以下选项。
--enable-hpa-rolling-update-skipped=true
```

## HPA到达阈值为何不进行扩缩容？

HPA的扩缩容的触发条件不仅是CPU利用率或内存使用率超过阈值与低于阈值，需要额外考虑的是如果扩容或者缩容后，是否会再次触发缩容或者扩容，减少震荡的场景。

## HPA采集周期如何配置？

对版本号大于v0.2.1-b46d98c-aliyun的metric-server，在metric-server的启动参数中设置`--metric_resolution`，例如`--metric_resolution=15s`即可。

## CronHPA是否兼容HPA？

CronHPA可以兼容HPA。阿里云容器服务ACK将CronHPA中的scaleTargetRef设置为HPA对象，然后通过HPA对象来寻找真实的scaleTargetRef，从而让CronHPA感知HPA的当前状态。CronHPA不会直接调整Deployment的副本数目，而是通过HPA来操作Deployment，这样可以避免HPA和CronHPA的冲突问题。关于CronHPA兼容HPA的更多信息，请参见[容器定时伸缩（CronHPA）](/intl.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器定时伸缩（CronHPA）.md)。

## 如何解决HPA启动时CPU或内存飙高造成扩容出多余Pod的多弹现象？

对于Java等需要预热的语言与框架而言，在容器刚启动的时候，有可能会出现分钟级别的CPU、内存飙高，其可能会造成HPA的误触发。您可以通过升级ACK提供的最新版metrics-server组件，并在Pod的Annotation上增加开关防止误触发。关于如何升级metrics-server组件，请参见[升级Kubernetes集群的metrics-server组件](/intl.zh-CN/Kubernetes集群用户指南/集群/管理集群/升级Kubernetes集群的metrics-server组件.md)。

增加开关防止误触发部署示例YAML如下。

```
## 以Deployment为例
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment-basic
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      annotations:
        HPAScaleUpDelay: 3m # m代表分钟，表示HPA在Pod创建3分钟后开始生效，可选单位为[s(秒),m(分)]。
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9 # Replace it with your exactly <image_name:tags>.
        ports:
        - containerPort: 80 
```

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

        如果确认alibaba-cloud-metrics-adapter状态正常，请确认HPA指标是否是Ingress相关指标。如果是Ingress相关指标，您需要提前部署日志服务组件。更多信息，请参见[Ingress访问日志分析与监控](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress访问日志分析与监控.md)。

    3.  确认HPA指标填写正确。sls.ingress.route的值格式为`<namespace>-<svc>-<port>`。
        -   namespace为Ingress所在的命名空间。
        -   svc是Ingress对应的Service名称。
        -   port是Ingress对应Service的端口名称。
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


[metrics-server](/intl.zh-CN/产品发布记录/组件介绍与变更记录/日志与监控/metrics-server.md)

[容器水平伸缩（HPA）](/intl.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器水平伸缩（HPA）.md)

[容器定时伸缩（CronHPA）](/intl.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器定时伸缩（CronHPA）.md)

[升级Kubernetes集群的metrics-server组件](/intl.zh-CN/Kubernetes集群用户指南/集群/管理集群/升级Kubernetes集群的metrics-server组件.md)

