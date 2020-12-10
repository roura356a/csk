---
keyword: [定时伸缩CronHPA, HPA, 兼容]
---

# 定时伸缩CronHPA兼容HPA

定时伸缩CronHPA通过设置定时的方式触发容器的水平副本伸缩。为了防止突发的流量冲击等状况，您可能已经或者打算配置HPA保障应用的正常运行。如果同时检测到了HPA和CronHPA的存在，阿里云容器服务ACK会将HPA作为定时伸缩CronHPA的扩缩容对象，从而实现对该HPA定义的Deployment对象的定时扩缩容。本文介绍定时伸缩CronHPA兼容HPA的原理。

## CronHPA和HPA的冲突问题

CronHPA和HPA的定义模板如下。



对比上述CronHPA和HPA资源的定义模板，可以看出：

-   CronHPA和HPA都是通过scaleTargetRef字段来获取伸缩对象。
-   CronHPA通过jobs的crontab规则定时伸缩副本数。
-   HPA通过资源利用率判断伸缩的情况。

设想如果同时设置CronHPA和HPA，那么会出现CronHPA和HPA同时操作一个scaleTargetRef的场景。因为CronHPA和HPA相互独立无法感知，所以就会出现两个Controller各自工作，后执行的操作会覆盖先执行的操作。

## CronHPA和HPA的冲突解决方案

从上文可知，CronHPA和HPA同时使用会导致后执行的操作覆盖先执行操作的本质原因是两个controller无法相互感知，那么只需要让CronHPA感知HPA的当前状态就能解决冲突问题。HPA的定义将Deployment配置在scaleTargetRef字段下，然后Deployment通过自身定义查找ReplicaSet，最后通过ReplicaSet调整真实的副本数目。阿里云容器服务ACK将CronHPA中的scaleTargetRef设置为HPA对象，然后通过HPA对象来寻找真实的scaleTargetRef，从而让CronHPA感知HPA的当前状态。

![cronhpa](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2500057061/p188680.png)

CronHPA兼容HPA的定义模板如下。

```
apiVersion: autoscaling.alibabacloud.com/v1beta1
kind: CronHorizontalPodAutoscaler
metadata:
  labels:
    controller-tools.k8s.io: "1.0"
  name: cronhpa-sample
spec:
   scaleTargetRef:
      apiVersion: autoscaling/v1
      kind: HorizontalPodAutoscaler
      name:  nginx-deployment-basic-hpa
   jobs:
   - name: "scale-down"
     schedule: "30 */1 * * * *"
     targetSize: 1
     runOnce: true
   - name: "scale-up"
     schedule: "0 */1 * * * *"
     targetSize: 3
     runOnce: true
```

通过上述CronHPA的定义，CronHPA可以明确知晓HPA中的minReplicas、maxReplicas、desiredReplicas的数值，但是同时也知晓HPA中scaleTargetRef所对应的当前Relicas值。CronHPA会通过调整HPA的方式感知HPA。CronHPA通过识别要达到的副本数与当前副本数两者间的较大值，判断是否需要扩缩容及修改HPA的上限；CronHPA通过识别CronHPA要达到的副本数与HPA的配置间的较小值，判断是否需要修改HPA的下限。

以下根据不同的场景，解释CronHPA兼容HPA的规则。

|HPA（min/max）|Cronhpa|Deployment|扩缩结果|兼容规则说明|
|------------|-------|----------|----|------|
|1/10|5|5|-   HPA（min/max）：1/10
-   Deployment：5

|当CronHPA中的目标副本数和当前副本数一致时，HPA中的最大和最小副本数，还有应用当前的副本数无需变更。|
|1/10|4|5|-   HPA（min/max）：1/10
-   Deployment：5

|当CronHPA中的目标副本数低于当前副本数时，保留当前副本数。|
|1/10|6|5|-   HPA（min/max）：6/10
-   Deployment：6

|-   当CronHPA中的目标副本数高于当前副本数时，保留CronHPA的目标副本数。
-   CronHPA目标副本数高于HPA副本数下限（minReplicas）时，修改HPA的副本数下限。 |
|5/10|4|5|-   HPA（min/max）：4/10
-   Deployment：5

|-   当CronHPA中的目标副本数低于当前副本数时，保留当前应用副本数。
-   CronHPA目标副本数低于HPA副本数下限（minReplicas）时，修改HPA的副本数下限。 |
|5/10|11|5|-   HPA（min/max）：11/11
-   Deployment：11

|-   当CronHPA中的目标副本数高于当前副本数时，保留CronHPA的目标副本数。
-   CronHPA目标副本数高于HPA副本数上限（maxReplicas）时，修改HPA的副本数上限。 |

表格中参数说明如下：

-   HPA（min/max）：表示HPA定义中最小和最大的副本数值。
-   CronHPA：表示预期扩缩容的副本数。
-   Deployment：表示当前应用的副本数。

从上文看出，CronHPA不会直接调整Deployment的副本数目，而是通过HPA来操作Deployment，这样可以避免HPA和CronHPA的冲突问题。

**相关文档**  


[容器定时伸缩](/intl.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器定时伸缩.md)

[容器水平伸缩（HPA）](/intl.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器水平伸缩（HPA）.md)

