---
keyword: [定时扩容, cronhpa, kubernetes]
---

# 容器定时伸缩（CronHPA）

应对资源浪费的场景，阿里云服务容器提供kubernetes-cronhpa-controller组件，实现资源定时扩容。本文介绍如何实现容器的定时伸缩。

-   您已创建一个容器服务Kubernetes集群，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。
-   在本地计算机上安装Helm，请参见按照[Install Helm](https://github.com/helm/helm?spm=a2c4g.11186623.2.28.36d61fa8fxvUaY)，并确保Helm版本v2.11.0以上，请参见[手动升级Helm]()。
-   在本地使用命令前，您需要先设置[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。

kubernetes-cronhpa-controller是一个Kubernetes HPA controller，按照类似crontab的策略定时地对容器服务Kubernetes集群进行扩缩容。您可以把CronHorizontalPodAutoscaler（以下简称cronhpa）用在任何Kubernetes中定义的对象上，只要该对象支持scale子资源（如，Deployment和StatefulSet）的[项目](https://github.com/AliyunContainerService/kubernetes-cronhpa-controller)已在GitHub开源。

以下举例对cronhpa的各字段进行解释说明。

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
|scaleTargetRef|scaleTargetRef指定去扩缩容对象。如果对象支持scale子资源，cronhpa即可支持。|
|excludeDates|excludeDates是日期数组。当遇到符合excludeDates描述的日期时任务将会被跳过。 **说明：** 最小单位为**天**。

`- "* * * * * *"`表示-"Seconds Minutes Hours Day of month Month Day of week"。

如您想在11月15日不运行任务，可像以下示例一样指定excludeDates。 ```
excludeDates:
  - "* * * 15 11 *"
``` |
|jobs|支持在一个spec中设定多个cronhpa任务。每个cronhpa任务可以配置以下字段： -   name：它在一个cronhpa中应是唯一的，这样就可以通过name来区分不同的任务。
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

## 安装kubernetes-cronhpa-controller

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com/)。
2.  单击左侧导航栏中的**市场** \> **应用目录**。
3.  在**阿里云应用**页面右上方**名称**搜索框中输入kubernetes-cronhpa-controller，然后单击**ack-kubernetes-cronhpa-controller**组件。
4.  在**应用目录-ack-kubernetes-cronhpa-controller**页面右侧的**创建**区域，填写相关创建信息。
    -   **集群**：选择目标集群。资源对象将部署在该集群内。
    -   **命名空间**：选择资源对象所属的命名空间，默认是 default。除了节点、持久化存储卷等底层计算资源以外，大多数资源对象需要作用于命名空间。
    -   **发布名称**：显示资源对象的发布名称。
5.  单击**创建**，安装kubernetes-cronhpa-controller。

## 示例1 Cronhpa独立工作

成功安装kubernetes-cronhpa-controller组件后，以下举例如何部署具体的cronhpa。

1.  执行下面的命令，部署示例对象与cronhpa。

    ```
    kubectl apply -f deployment_cronhpa.yaml
    ```

    deployment\_cronhpa.yaml如下：

    ```
    ---
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
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9 # replace it with your exactly <image_name:tags>
            ports:
            - containerPort: 80
    ---
    apiVersion: autoscaling.alibabacloud.com/v1beta1
    kind: CronHorizontalPodAutoscaler
    metadata:
      labels:
        controller-tools.k8s.io: "1.0"
      name: cronhpa-sample
    spec:
       scaleTargetRef:
          apiVersion: apps/v1
          kind: Deployment
          name: nginx-deployment-basic
       jobs:
       - name: "scale-down"
         schedule: "30 */1 * * * *"
         targetSize: 1
       - name: "scale-up"
         schedule: "0 */1 * * * *"
         targetSize: 3
    ```

2.  执行以下命令，检查示例应用副本数目。

    ```
    kubectl get deploy nginx-deployment-basic 
    ```

    ```
    ➜  kubernetes-cronhpa-controller git:(master) ✗ kubectl get deploy nginx-deployment-basic
    NAME                     DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    nginx-deployment-basic   2         2         2            2           9s
    ```

3.  执行`kubectl describe cronhpa cronhpa-sample`命令，查看cronhpa的状态，确认cronhpa的job已提交。

    ```
    Name:         cronhpa-sample
    Namespace:    default
    Labels:       controller-tools.k8s.io=1.0
    Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                    {"apiVersion":"autoscaling.alibabacloud.com/v1beta1","kind":"CronHorizontalPodAutoscaler","metadata":{"annotations":{},"labels":{"controll...
    API Version:  autoscaling.alibabacloud.com/v1beta1
    Kind:         CronHorizontalPodAutoscaler
    Metadata:
      Creation Timestamp:  2019-04-14T10:42:38Z
      Generation:          1
      Resource Version:    4017247
      Self Link:           /apis/autoscaling.alibabacloud.com/v1beta1/namespaces/default/cronhorizontalpodautoscalers/cronhpa-sample
      UID:                 05e41c95-5ea2-11e9-8ce6-00163e12e274
    Spec:
      Jobs:
        Name:         scale-down
        Schedule:     30 */1 * * * *
        Target Size:  1
        Name:         scale-up
        Schedule:     0 */1 * * * *
        Target Size:  3
      Scale Target Ref:
        API Version:  apps/v1
        Kind:         Deployment
        Name:         nginx-deployment-basic
    Status:
      Conditions:
        Job Id:           38e79271-9a42-4131-9acd-1f5bfab38802
        Last Probe Time:  2019-04-14T10:43:02Z
        Message:
        Name:             scale-down
        Schedule:         30 */1 * * * *
        State:            Submitted
        Job Id:           a7db95b6-396a-4753-91d5-23c2e73819ac
        Last Probe Time:  2019-04-14T10:43:02Z
        Message:
        Name:             scale-up
        Schedule:         0 */1 * * * *
        State:            Submitted
    Events:               <none>
    ```

    **说明：** 以上示例中出现的代码，请参见[examples目录](https://github.com/AliyunContainerService/kubernetes-cronhpa-controller/blob/master/examples)。


1.  执行下面的命令，部署示例对象、HPA及cronhpa。

    ```
    kubectl apply -f deployment_cronhpa_hpa.yaml
    ```

    deployment\_cronhpa\_hpa.yaml如下：

    ```
    ---
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
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9 # replace it with your exactly <image_name:tags>
            resources:
              requests:
                cpu: 50m
                memory: 60m
            ports:
            - containerPort: 80
    ---
    apiVersion: autoscaling/v1
    kind: HorizontalPodAutoscaler
    metadata:
      name: nginx-deployment-basic-hpa
      namespace: default
    spec:
      scaleTargetRef:
        apiVersion: apps/v1
        kind: Deployment
        name: nginx-deployment-basic
      minReplicas: 1
      maxReplicas: 10
      targetCPUUtilizationPercentage: 50
    ---
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

2.  执行以下命令，检查示例应用副本数目及HPA。

    ```
    kubectl get deploy nginx-deployment-basic
    ```

    ```
    NAME                     DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    nginx-deployment-basic   2         2         2            2           9s
    -------
    kubectl get hpa  nginx-deployment-basic-hpa
    NAME                         REFERENCE                           TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
    nginx-deployment-basic-hpa   Deployment/nginx-deployment-basic   0%/50%    1         10        2          150m
    ```

3.  执行`kubectl describe cronhpa cronhpa-sample`命令，查看cronhpa的状态 。

    ```
    Name:         cronhpa-sample
    Namespace:    default
    Labels:       controller-tools.k8s.io=1.0
    Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                    {"apiVersion":"autoscaling.alibabacloud.com/v1beta1","kind":"CronHorizontalPodAutoscaler","metadata":{"annotations":{},"labels":{"contro
    ll...
    API Version:  autoscaling.alibabacloud.com/v1beta1
    Kind:         CronHorizontalPodAutoscaler
    Metadata:
      Creation Timestamp:  2020-04-15T06:02:26Z
      Generation:          32
      Resource Version:    45484074
      Self Link:           /apis/autoscaling.alibabacloud.com/v1beta1/namespaces/default/cronhorizontalpodautoscalers/cronhpa-sample
      UID:                 ae9c547e-7ede-11ea-8a4c-00163e080212
    Spec:
      Exclude Dates:  <nil>
      Jobs:
        Name:         scale-down
        Run Once:     false
        Schedule:     30 */1 * * * *
        Target Size:  1
        Name:         scale-up
        Run Once:     false
        Schedule:     0 */1 * * * *
        Target Size:  3
      Scale Target Ref:
        API Version:  autoscaling/v1
        Kind:         HorizontalPodAutoscaler
        Name:         nginx-deployment-basic-hpa
    Status:
      Conditions:
        Job Id:           6d5dca76-d120-4d2a-b154-2c39a6650241
        Last Probe Time:  2020-04-15T09:36:30Z
        Message:          cron hpa job scale-down executed successfully. Skip scale replicas because HPA nginx-deployment-basic-hpa current replicas:3 >= de
    sired replicas:1.
        Name:             scale-down
        Run Once:         false
        Schedule:         30 */1 * * * *
        State:            Succeed
        Target Size:      1
        Job Id:           26a6176f-c476-4efa-bcf9-3388c6fd6059
        Last Probe Time:  2020-04-15T09:36:00Z
        Message:          cron hpa job scale-up executed successfully. current replicas:2, desired replicas:3.
        Name:             scale-up
        Run Once:         false
        Schedule:         0 */1 * * * *
        State:            Succeed
        Target Size:      3
      Exclude Dates:      <nil>
      Scale Target Ref:
        API Version:  autoscaling/v1
        Kind:         HorizontalPodAutoscaler
        Name:         nginx-deployment-basic-hpa
    Events:
      Type    Reason   Age                  From                            Message
      ----    ------   ----                 ----                            -------
      Normal  Succeed  31s (x4 over 3h33m)  cron-horizontal-pod-autoscaler  cron hpa job scale-up executed successfully. current replicas:2, desired replica
    s:3.
      Normal  Succeed  1s (x8 over 121m)    cron-horizontal-pod-autoscaler  cron hpa job scale-down executed successfully. Skip scale replicas because HPA n
    ginx-deployment-basic-hpa current replicas:3 >= desired replicas:1.
    ```


如果cronhpa任务的State是Succeed，则说明最近一次的执行是成功的；如果是Submitted则意味着cronhpa任务已经提交给cron engine，但是截至当前还未执行。等待30秒后可再次查询状态，如果所有任务的State都为Succeed，则全部运行成功。

**相关文档**  


[容器水平伸缩（HPA）](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器水平伸缩（HPA）.md)

[定时伸缩CronHPA兼容HPA](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/定时伸缩CronHPA兼容HPA.md)

