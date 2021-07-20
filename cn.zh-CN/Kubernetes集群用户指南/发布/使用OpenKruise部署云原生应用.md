---
keyword: [OpenKruise, CloneSet, Advanced StatefulSet]
---

# 使用OpenKruise部署云原生应用

OpenKruise是Kubernetes的一个标准扩展，可以配合原生Kubernetes使用。OpenKruise核心在于自动化，从不同维度解决Kubernetes上应用的自动化，包括部署、升级、弹性扩缩容等。本文介绍如何使用OpenKruise部署云原生应用。

OpenKruise是阿里云开源的云原生应用自动化引擎，也是阿里巴巴经济体上云全面使用的部署基座，已正式加入CNCF Sandbox。

OpenKruise包含了多种自定义Workload，用于无状态应用、有状态应用、Sidecar容器、Daemon应用等部署管理，提供了原地升级、灰度、流式、配置优先级等扩展策略。

## 使用说明

OpenKruise包含CloneSet、Advanced StatefulSet、Advanced DaemonSet等控制器。以下介绍常用控制器的功能。

|控制器|功能|推荐指数|
|---|--|----|
|CloneSet|管理无状态应用，对标Kubernetes原生Deployment。关于CloneSet的详细介绍，请参见[Cloneset](https://openkruise.io/zh-cn/docs/cloneset.html)。

资源（YAML）的字段与Deployment不完全兼容，但功能上全覆盖，并提供比Deployment更丰富的策略。

|推荐指数：✩✩✩✩✩|
|Advanced StatefulSet|管理有状态应用，对标Kubernetes原生StatefulSet。关于Advanced StatefulSet的详细介绍，请参见[Advanced StatefulSet](https://openkruise.io/zh-cn/docs/advanced_statefulset.html)。

资源（YAML）字段与原生StatefulSet完全兼容，只需要把`apiVersion`改为`apps.kruise.io/v1alpha1`，另外提供了`optional`字段来扩展发布策略（原地升级、并行发布等）。

|推荐指数：✩✩✩✩|
|Advanced DaemonSet|管理Daemon应用，对标Kubernetes原生DaemonSet。关于Advanced DaemonSet的详细介绍，请参见[Advanced DaemonSet](https://openkruise.io/zh-cn/docs/advanced_daemonset.html)。

资源（YAML）字段与原生DaemonSet完全兼容，只需要把`apiVersion`改为`apps.kruise.io/v1alpha1`，另外提供了`optional`字段来扩展发布策略（热升级、灰度、按Node标签灰度等）。

|推荐指数：✩✩✩✩|
|SidecarSet|独立管理Sidecar容器和注入。关于SidecarSet的详细介绍，请参见[SidecarSet](https://openkruise.io/zh-cn/docs/sidecarset.html)。

在独立CR中定义Sidecar容器和Label Selector，OpenKruise会在所有符合Selector条件的Pod创建时注入定义好的Sidecar容器，并支持对已注入Sidecar容器做原地升级。

|推荐指数：✩✩✩✩|
|UnitedDeployment|管理不同区域下的多个Sub Workload，关于UnitedDeployment的详细介绍，请参见[UnitedDeployment](https://openkruise.io/zh-cn/docs/uniteddeployment.html)。

目前支持将CloneSet、StatefulSet、Advanced StatefulSet作为Sub Workload，您可以用一个UnitedDeployment来定义不同区域中的Sub Workload部署Replicas。

|推荐指数：✩✩✩|

以下介绍CloneSet、Advanced StatefulSet、Advanced DaemonSet与社区对标控制器的功能对比。

|功能|CloneSet VS Deployment|Advanced StatefulSet VS StatefulSet|Advanced DaemonSet VS DaemonSet|
|CloneSet|Deployment|Advanced StatefulSet|StatefulSet|Advanced DaemonSet|DaemonSet|
|--|----------------------|-----------------------------------|-------------------------------|
|--------|----------|--------------------|-----------|------------------|---------|
|流式扩容|不支持（开源中，未来会支持）|不支持|不支持|不支持|支持|不支持|
|指定缩容|支持|不支持|支持|不支持|不支持|不支持|
|Pod重建升级|支持|支持|支持|支持|支持|支持|
|Pod原地升级|支持|不支持|支持|不支持|不支持（开源中，未来会支持）|不支持|
|分批灰度发布|支持|不支持|支持|支持|支持|不支持|
|最大可用数量|支持|支持|支持|不支持|支持|支持|
|最大弹性数量|支持|支持|不支持|不支持|支持|不支持|
|通过使用优先级策略和打散策略来自定义发布顺序|支持|不支持|支持|不支持|支持|不支持|
|通过lifecycle hook管理Pod生命周期|支持|不支持|不支持|不支持|不支持|不支持|

## 安装OpenKruise

您可以通过ACK应用目录和Helm Chart安装OpenKruise，推荐使用Helm Chart安装OpenKruise。以下为两种安装方式对比：

**说明：** 安装OpenKruise前，请确保Kubernetes版本≥1.13。如果您使用的是Kubernetes 1.13或1.14版本，必须先在kube-apiserver中打开`CustomResourceWebhookConversion`feature-gate。

-   通过ACK应用目录安装：一键安装，无需使用Helm命令行工具，后续只能通过执行`helm upgrade`命令，升级OpenKruise版本。
-   通过Helm Chart安装：适用于所有原生Kubernetes集群，可自主管理版本、配置参数，但是需要命令行操作。

**通过ACK应用目录安装OpenKruise**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在**阿里云应用**页签下单击**应用管理**，然后选择**ack-kruise**。

4.  在应用目录 - ack-kruise页面**创建**区域，选择安装的**集群**和**命名空间**设置**发布名称**，然后单击**创建**。


**通过Helm Chart安装OpenKruise**

1.  安装Helm命令行工具。关于Helm工具的下载地址，请参见[Helm Release](https://github.com/helm/helm/releases)。

    **说明：** 请确保安装的Helm命令行工具的版本≥3.1.0。

2.  安装OpenKruise。

    **说明：** 如果您需要对OpenKruise进行特殊配置，请参见[安装OpenKruise](https://openkruise.io/zh-cn/docs/quick_start.html)。

    -   如果您使用的是Kubernetes 1.13或1.14版本，执行以下命令安装OpenKruise。

        ```
        # Kubernetes 1.13或1.14版本
        helm install kruise https://github.com/openkruise/kruise/releases/download/v0.7.0/kruise-chart.tgz --disable-openapi-validation
        ```

    -   如果您使用的是Kubernetes 1.15及以上版本，执行以下命令安装OpenKruise。

        ```
        # Kubernetes 1.15和更新的版本
        helm install kruise https://github.com/openkruise/kruise/releases/download/v0.7.0/kruise-chart.tgz
        ```


## 使用CloneSet部署无状态应用

1.  创建CloneSet。

    1.  创建cloneset.yaml。

        ```
        apiVersion: apps.kruise.io/v1alpha1
        kind: CloneSet
        metadata:
          name: demo-clone
        spec:
          replicas: 5
          selector:
            matchLabels:
              app: guestbook
          template: #pod template和deployment的结构完全一致。
            metadata:
              labels:
                app: guestbook
            spec:
              affinity:
                podAntiAffinity:
                  preferredDuringSchedulingIgnoredDuringExecution:
                  - podAffinityTerm:
                      labelSelector:
                        matchExpressions:
                        - key: app
                          operator: In
                          values:
                          - guestbook
                      topologyKey: kubernetes.io/hostname
                    weight: 100
              containers:
              - name: guestbook
                image: registry.cn-hangzhou.aliyuncs.com/kruise-test/guestbook:v1
                env:
                - name: test
                  value: foo
          updateStrategy:
            type: InPlaceIfPossible     #尽量使用原地升级，否则重建升级。
            maxUnavailable: 20%        #发布过程最多20%不可用。
            inPlaceUpdateStrategy:
              gracePeriodSeconds: 3    #每个Pod原地升级前not ready优雅等待时间。
        ```

        -   type：设置升级策略，支持以下3种升级方式。
            -   ReCreate：控制器会删除旧Pod和PVC，然后用新版本重新创建Pod和PVC。
            -   InPlaceIfPossible：控制器会优先尝试原地升级Pod，如果不行再采用重建升级。
            -   InPlaceOnly：控制器只允许采用原地升级。
        -   maxUnavailable：发布过程中，限制最多不可用的Pod数量，可以设置为一个绝对值或者百分比。
        -   gracePeriodSeconds：每个Pod原地升级前not ready优雅等待时间。
    2.  使cloneset.yaml在ACK集群中生效。

        ```
        kubectl create -f ./cloneset.yaml
        ```

        预期输出：

        ```
        cloneset.apps.kruise.io/demo-clone created
        ```

2.  查看Pod运行状态。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME               READY   STATUS    RESTARTS   AGE
    demo-clone-5b9kl   1/1     Running   0          3s
    demo-clone-6xjdg   1/1     Running   0          3s
    demo-clone-bvmdj   1/1     Running   0          3s
    demo-clone-dm22s   1/1     Running   0          3s
    demo-clone-rbpg9   1/1     Running   0          3s
    ```

3.  查看CloneSet。

    ```
    kubectl get clone
    ```

    预期输出：

    ```
    NAME         DESIRED   UPDATED   UPDATED_READY   READY   TOTAL   AGE
    demo-clone   5         5         5               5       5       46s
    ```

    -   DESIRED：期望Pod数量（spec.replicas）。
    -   UPDATED：最新版本的Pod数量（status.updatedReplicas）。
    -   UPDATED\_READY：最新版本的可用Pod数量（status.updatedReadyReplicas）。
    -   READY：可用Pod总量（status.readyReplicas）。
    -   TOTAL：实际Pod总量（status.replicas）。

## 使用Advanced StatefulSet部署有状态应用

1.  创建Advanced StatefulSet。

    1.  创建statefulset.yaml。

        ```
        apiVersion: apps.kruise.io/v1alpha1
        kind: StatefulSet
        metadata:
          name: demo-asts
        spec:
          replicas: 3
          selector:
            matchLabels:
              app: guestbook-sts
          podManagementPolicy: Parallel
          template: #Pod template和官方statefulset的结构完全一致。
            metadata:
              labels:
                app: guestbook-sts
            spec:
              affinity:
                podAntiAffinity:
                  preferredDuringSchedulingIgnoredDuringExecution:
                  - podAffinityTerm:
                      labelSelector:
                        matchExpressions:
                        - key: app
                          operator: In
                          values:
                          - guestbook-sts
                      topologyKey: kubernetes.io/hostname
                    weight: 100
              containers:
              - name: guestbook
                image: registry.cn-hangzhou.aliyuncs.com/kruise-test/guestbook:v1
                env:
                - name: test
                  value: foo
                volumeMounts:
                - name: log-volume
                  mountPath: /var/log
              readinessGates:
              - conditionType: InPlaceUpdateReady
              volumes:
              - name: log-volume
                emptyDir: {}
          updateStrategy:
            type: RollingUpdate
            rollingUpdate:
              podUpdatePolicy: InPlaceIfPossible #尽量使用原地升级，否则重建升级
              maxUnavailable: 20% #发布过程最多20%不可用。
              inPlaceUpdateStrategy:
                gracePeriodSeconds: 3 #每个Pod原地升级前not ready优雅等待时间。
        ```

        -   type：设置Pod升级策略，支持以下3种方式升级。
            -   ReCreate：控制器会删除旧Pod和PVC，然后用新版本重新创建Pod和PVC。
            -   InPlaceIfPossible：控制器会优先尝试原地升级Pod，如果不行再采用重建升级。
            -   InPlaceOnly：控制器只允许采用原地升级。
        -   maxUnavailable：发布过程中，限制最多不可用的Pod数量，可以设置为一个绝对值或者百分比。
        -   gracePeriodSeconds：每个Pod原地升级前not ready优雅等待时间。
    2.  使statefulset.yaml在ACK集群中生效。

        ```
         kubectl create -f ./statefulset.yaml
        ```

        预期输出：

        ```
        statefulset.apps.kruise.io/demo-asts created
        ```

2.  查看Pod运行状态。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME          READY   STATUS    RESTARTS   AGE
    demo-asts-0   1/1     Running   0          3h29m
    demo-asts-1   1/1     Running   0          3h29m
    demo-asts-2   1/1     Running   0          3h29m
    ```

3.  查看Advanced StatefulSet。

    ```
    kubectl get asts
    ```

    预期输出：

    ```
    NAME        DESIRED   CURRENT   UPDATED   READY   AGE
    demo-asts   3         3         3         3       3h30m
    ```

    -   DESIRED：期望Pod数量（spec.replicas）。
    -   UPDATED：最新版本的Pod数量（status.updatedReplicas）。
    -   READY：可用Pod总量（status.readyReplicas）。
    -   TOTAL：实际Pod总量（status.replicas）。

**相关文档**  


[OpenKruise官网文档](https://openkruise.io/zh-cn/docs/what_is_openkruise.html)

[OpenKruise Github](https://github.com/openkruise/kruise)

