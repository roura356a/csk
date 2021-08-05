---
keyword: [Descheduler组件, 调度优化]
---

# 使用Descheduler组件对Pod进行调度优化

为了解决实际运行中集群资源无法充分利用或浪费的问题，可以使用Descheduler组件对集群的Pod进行调度优化，使部分不合理Pod能够重新调度到合适的位置上。本文介绍如何配置使用Descheduler组件。

## 前提条件

-   已创建ACK集群（版本不低于1.14）。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已安装Helm3。具体操作，请参见[Install Helm](https://github.com/helm/helm)。
-   已通过kubectl工具连接kubernetes集群。具体操作，请参见[通过kubectl管理Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)。

## 操作步骤

1.  使用Helm安装Descheduler。

    1.  执行以下命令，以Helm方式添加Descheduler的Chart仓库。

        ```
        helm repo add descheduler https://kubernetes-sigs.github.io/descheduler/
        ```

        预期输出：

        ```
        "descheduler" has been added to your repositories
        ```

    2.  执行以下命令，安装Descheduler。

        ```
        helm install descheduler --namespace kube-system descheduler/descheduler
        ```

        预期输出：

        ```
        NAME: descheduler
        LAST DEPLOYED: Thu Mar  4 19:29:23 2021
        NAMESPACE: kube-system
        STATUS: deployed
        REVISION: 1
        TEST SUITE: None
        NOTES:
        Descheduler installed as a cron job.
        ```

        **说明：** 默认安装的Descheduler镜像可能由于网络原因出现拉取超时，可以使用阿里云镜像仓库中的`registry.cn-hangzhou.aliyuncs.com/carsnow/descheduler:v0.20.0`替换原有的镜像。

2.  确认安装状态。

    在Chart安装完成后，默认会在`kube-system`的`namespace`下部署执行周期为两分钟的Cronjob。

    ```
    kubectl get cronjob -n kube-system
    ```

    预期输出：

    ```
    NAME          SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
    descheduler   */2 * * * *   False     0        21s             47h
    ```

3.  查看当前的调度策略（DeschedulerPolicy）。

    ```
    kubectl describe cm descheduler -n kube-system
    ```

    预期输出：

    ```
    Name:         descheduler
    Namespace:    kube-system
    Labels:       app.kubernetes.io/instance=descheduler
                  app.kubernetes.io/managed-by=Helm
                  app.kubernetes.io/name=descheduler
                  app.kubernetes.io/version=0.20.0
                  helm.sh/chart=descheduler-0.20.0
    Annotations:  meta.helm.sh/release-name: descheduler
                  meta.helm.sh/release-namespace: kube-system
    Data
    ====
    policy.yaml:
    ----
    apiVersion: "descheduler/v1alpha1"
    kind: "DeschedulerPolicy"
    strategies:
      "RemoveDuplicates":
         enabled: true
      "RemovePodsViolatingInterPodAntiAffinity":
         enabled: true
      "LowNodeUtilization":
         enabled: true
         params:
           nodeResourceUtilizationThresholds:
             thresholds:
               "cpu" : 20
               "memory": 20
               "pods": 20
             targetThresholds:
               "cpu" : 50
               "memory": 50
               "pods": 50
      "RemovePodsHavingTooManyRestarts":
         enabled: true
         params:
           podsHavingTooManyRestarts:
             podRestartThreshold: 100
             includingInitContainers: true
    Events:  <none>
    ```

    关于`strategies`中策略的含义，请参见[Descheduler](https://github.com/kubernetes-sigs/descheduler)。

4.  查看调度策略修改前的调度效果。

    1.  部署测试Deployment。

        nginx.yaml的命令示例如下所示。

        ```
        apiVersion: apps/v1 # for versions before 1.8.0 use apps/v1beta1
        kind: Deployment
        metadata:
          name: nginx-deployment-basic
          labels:
            app: nginx
        spec:
          replicas: 3
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
        ```

        执行以下命令部署Deployment nginx.yaml。

        ```
        kubectl apply -f nginx.yaml
        ```

        预期输出：

        ```
        deployment.apps/nginx-deployment-basic created
        ```

    2.  等待两分钟后，执行以下命令，查看Pod分布节点。

        ```
        kubectl get pod -o wide | grep nginx
        ```

        预期输出：

        ```
        NAME                          READY   STATUS     RESTARTS   AGE    IP               NODE                         NOMINATED NODE   READINESS GATES
        nginx-deployment-basic-**1    1/1     Running    0          36s    172.25.XXX.XX1   cn-hangzhou.172.16.XXX.XX2   <none>           <none>
        nginx-deployment-basic-**2    1/1     Running    0          11s    172.25.XXX.XX2   cn-hangzhou.172.16.XXX.XX3   <none>           <none>
        nginx-deployment-basic-**3    1/1     Running    0          36s    172.25.XXX.XX3   cn-hangzhou.172.16.XXX.XX3   <none>           <none>
        ```

        从以上预期输出可以得出，`nginx-deployment-basic-**2`及`nginx-deployment-basic-**3`都被调度到`cn-hangzhou.172.16.XXX.XX3`节点。

5.  修改调度策略。

    为了防止多个调度策略影响调度效果，修改[步骤3](#step_aly_k85_ulh)中的configmap，只保留RemoveDuplicates策略。

    **说明：** RemoveDuplicates策略是指尽可能保障有副本控制器的Pod，可以水平分布到不同的节点上。

6.  查看调度策略修改后的调度效果。

    1.  部署新的调度策略。

        newPolicy.yaml的命令示例如下所示。

        ```
        apiVersion: v1
        kind: ConfigMap
        metadata:
          name: descheduler
          namespace: kube-system
          labels:
            app.kubernetes.io/instance: descheduler
            app.kubernetes.io/managed-by: Helm
            app.kubernetes.io/name: descheduler
            app.kubernetes.io/version: 0.20.0
            helm.sh/chart: descheduler-0.20.0  
          annotations:
            meta.helm.sh/release-name: descheduler
            meta.helm.sh/release-namespace: kube-system
        data: 
          policy.yaml: |-
            apiVersion: "descheduler/v1alpha1"
            kind: "DeschedulerPolicy"
            strategies:
              "RemoveDuplicates":
                 enabled: true
        ```

        执行以下命令部署Deployment newPolicy.yaml。

        ```
        kubectl apply -f newPolicy.yaml
        ```

        预期输出：

        ```
        configmap/descheduler created
        ```

    2.  等待两分钟后，执行以下命令，查看Pod分布节点。

        ```
        kubectl get pod -o wide | grep nginx
        ```

        预期输出：

        ```
        NAME                          READY   STATUS     RESTARTS   AGE      IP               NODE                         NOMINATED NODE   READINESS GATES
        nginx-deployment-basic-**1    1/1     Running    0          8m26s    172.25.XXX.XX1   cn-hangzhou.172.16.XXX.XX2   <none>           <none>
        nginx-deployment-basic-**2    1/1     Running    0          8m1s     172.25.XXX.XX2   cn-hangzhou.172.16.XXX.XX1   <none>           <none>
        nginx-deployment-basic-**3    1/1     Running    0          8m26s    172.25.XXX.XX3   cn-hangzhou.172.16.XXX.XX3   <none>           <none>
        ```

        从以上预期输出可以得出，`nginx-deployment-basic-**2`被Descheduler重新调度到`cn-hangzhou.172.16.XXX.XX1`节点，三个Pod被分散到三个不同的Node节点，实现了副本之间在不同节点的均衡分布。


