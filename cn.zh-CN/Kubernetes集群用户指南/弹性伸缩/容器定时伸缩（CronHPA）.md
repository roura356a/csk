---
keyword: [定时扩容, cronhpa, kubernetes, 兼容HPA]
---

# 容器定时伸缩（CronHPA）

应对资源浪费的场景，阿里云服务容器提供kubernetes-cronhpa-controller组件，实现资源定时扩容。本文介绍如何实现容器的定时伸缩，及介绍定时伸缩CronHPA兼容HPA的原理。

-   您已创建一个容器服务Kubernetes集群，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   在本地计算机上安装Helm，请参见按照[Install Helm](https://github.com/helm/helm?spm=a2c4g.11186623.2.28.36d61fa8fxvUaY)，并确保Helm版本v2.11.0以上。
-   在本地使用命令前，您需要先设置[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

kubernetes-cronhpa-controller是一个Kubernetes HPA controller，按照类似Crontab的策略定时地对容器服务Kubernetes集群进行扩缩容。您可以把CronHorizontalPodAutoscaler（以下简称CronHPA）用在任何Kubernetes中定义的对象上，只要该对象支持伸缩子资源（如Deployment和StatefulSet）的[项目](https://github.com/AliyunContainerService/kubernetes-cronhpa-controller)已在GitHub开源。

以下举例对CronHPA的各字段进行解释说明。

```
apiVersion: autoscaling.alibabacloud.com/v1beta1
kind: CronHorizontalPodAutoscaler
metadata:
  labels:
    controller-tools.k8s.io: "1.0"
  name: cronhpa-sample
  namespace: default 
spec:
   scaleTargetRef:
      apiVersion: apps/v1
      kind: Deployment
      name: nginx-deployment-basic
   excludeDates:
   # exclude November 15th
   - "* * * 15 11 *"
   # exclude every Friday 
   - "* * * * * 5"
   jobs:
   - name: "scale-down"
     schedule: "30 */1 * * * *"
     targetSize: 1
   - name: "scale-up"
     schedule: "0 */1 * * * *"
     targetSize: 3
     runOnce: true
```

|字段|说明|
|--|--|
|scaleTargetRef|scaleTargetRef指定去扩缩容对象。如果对象支持scale子资源，CronHPA即可支持。|
|excludeDates|excludeDates是日期数组。当遇到符合excludeDates描述的日期时任务将会被跳过。 **说明：** 最小单位为**天**。

`- "* * * * * *"`表示-"<seconds\> <minutes\> <hours\> <days of month\> <months\>"。

如您想在11月15日不运行任务，可像以下示例一样指定excludeDates。 ```
excludeDates:
  - "* * * 15 11 *"
``` |
|jobs|支持在一个spec中设定多个CronHPA任务。每个CronHPA任务可以配置以下字段： -   name：它在一个CronHPA中应是唯一的，这样就可以通过name来区分不同的任务。
-   schedule：它的策略和crontab类似。kubernetes-cronhpa-controller使用了Golang库（[go-cron](https://github.com/ringtail/go-cron)）功能更强大的cron，它支持更多表示规则。创建时请严格按照以下格式说明进行设置，否则将导致创建失败。

cron表达式的格式如下：

    ```
Field name   | Mandatory? | Allowed values  | Allowed special characters
  ----------   | ---------- | --------------  | --------------------------
  Seconds      | Yes        | 0-59            | * / , -
  Minutes      | Yes        | 0-59            | * / , -
  Hours        | Yes        | 0-23            | * / , -
  Day of month | Yes        | 1-31            | * / , - ?
  Month        | Yes        | 1-12 or JAN-DEC | * / , -
  Day of week  | Yes        | 0-6 or SUN-SAT  | * / , - ?
    ```

-   targetSize：到调度时间时，您想扩缩容到的Pod的数目。
-   runOnce：如果runOnce设置为true，那么任务将只执行一次，第一次执行完则exit。 |

## 安装CronHPA组件

您可以通过以下三种方式安装CronHPA组件ack-kubernetes-cronhpa-controller。

**方式一：通过组件管理入口安装CronHPA组件。**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **组件管理**。

5.  在组件管理页面单击**其他**页签，找到**ack-kubernetes-cronhpa-controller**，然后单击**安装**。


**方式二：通过系统组件管理入口安装CronHPA组件。**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群列表页面，选择目标集群，并在目标集群右侧**操作**列下，选择**更多** \> **系统组件管理**。

5.  在组件管理页面单击**其他**页签，找到**ack-kubernetes-cronhpa-controller**，然后单击**安装**。


**方式三：通过应用目录入口安装CronHPA组件。**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在**阿里云应用**页面右上方**名称**搜索框中输入kubernetes-cronhpa-controller，然后单击**ack-kubernetes-cronhpa-controller**组件。

4.  在**应用目录-ack-kubernetes-cronhpa-controller**页面右侧的**创建**区域，选择目标集群，然后单击**创建**。


## 创建定时伸缩CronHPA任务

为应用创建和运行CronHPA前，请确保集群中的CronHPA组件已正常运行，且当前应用只有一个HPA对象。关于CronHPA和HPA的兼容策略，请参见下文的[定时伸缩CronHPA兼容HPA](#section_ah0_3o3_h28)。您可以在以下两种场景中创建CronHPA任务。

**场景一：在创建应用时创建CronHPA任务。**

在创建应用的**高级配置**页面的**伸缩配置**区域，选中**定时伸缩**右侧**开启**为应用创建定时伸缩任务。关于创建应用的详细步骤，请参见[创建无状态工作负载Deployment](/cn.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建无状态工作负载Deployment.md)或者[创建有状态工作负载StatefulSet](/cn.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建有状态工作负载StatefulSet.md)。

![cronhpa](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1047219061/p206611.png)

容器服务管理控制台会自动检查是否已安装CronHPA组件。如果CronHPA组件未被安装，页面提示**点击安装**。安装CronHPA组件后，页面显示定时伸缩任务的创建配置。配置参数的详细描述如下：

|参数|描述|
|--|--|
|定时任务名称|为定时任务定义名称。每个任务的名称是唯一的，不能与其他任务名称重复。|
|目标副本数|当到达设定计划时间时，应用副本数自动伸缩至该值。|
|调度周期|设置调度周期。关于为定时伸缩任务设置调度周期的详细描述，请参见[AliyunContainerService/kubernetes-cronhpa-controller](https://github.com/AliyunContainerService/kubernetes-cronhpa-controller)。 |

**场景二：为已有应用创建CronHPA任务。**

本文以为无状态应用创建定时伸缩任务为例，说明如何为已有应用创建CronHPA任务。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在无状态页面，单击应用右侧**操作**列下的**详情**。

6.  单击**容器伸缩**页签，配置定时伸缩任务。

    -   如果CronHPA组件未被安装，页面提示**点击安装**。单击**点击安装**后，然后进行以下步骤。
    -   如果CronHPA组件已被安装，直接进行以下步骤。
7.  单击**定时伸缩（CronHPA）**右侧**创建**，然后在**创建**对话框中，设置定时伸缩任务的创建参数。

    ![create](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1047219061/p206630.png)

    关于创建定时伸缩任务的配置信息，请参见上文的[表 1](#table_3xu_x42_mfb)。


## 添加或修改定时伸缩任务

1.  请参见上文的[为已有应用创建CronHPA任务](#p_cfx_lt5_l3v)的步骤进入**容器伸缩**页面。

2.  在**容器伸缩**页签的**定时伸缩（CronHPA）**区域，单击目标任务右侧**操作**下的**任务添加或编辑**。

3.  在**编辑**对话框中，单击**添加任务**进行任务创建或者点击已有CronHPA任务中进行修改，然后单击**确定**。

    ![modify](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1047219061/p206651.png)

4.  您可以进行以下操作删除CronHPA任务。
5.  在**编辑**对话框中，单击任务名称右上角的删除图标，然后单击**确定**。

    ![delete](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1047219061/p206655.png)


## 定时伸缩CronHPA兼容HPA

定时伸缩CronHPA通过设置定时的方式触发容器的水平副本伸缩。为了防止突发的流量冲击等状况，您可能已经或者打算配置HPA保障应用的正常运行。如果同时检测到了HPA和CronHPA的存在，阿里云容器服务ACK会将HPA作为定时伸缩CronHPA的扩缩容对象，从而实现对该HPA定义的Deployment对象的定时扩缩容。

CronHPA和HPA的定义模板如下。



对比上述CronHPA和HPA资源的定义模板，可以看出：

-   CronHPA和HPA都是通过scaleTargetRef字段来获取伸缩对象。
-   CronHPA通过jobs的crontab规则定时伸缩副本数。
-   HPA通过资源利用率判断伸缩的情况。

设想如果同时设置CronHPA和HPA，那么会出现CronHPA和HPA同时操作一个scaleTargetRef的场景。因为CronHPA和HPA相互独立无法感知，所以就会出现两个Controller各自工作，后执行的操作会覆盖先执行的操作。

**CronHPA和HPA的兼容解决方案**

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

从上述看出，CronHPA不会直接调整Deployment的副本数目，而是通过HPA来操作Deployment，这样可以避免HPA和CronHPA的冲突问题。

**相关文档**  


[容器水平伸缩（HPA）](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器水平伸缩（HPA）.md)

