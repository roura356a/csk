---
keyword: [弹性配额组, Capacity Scheduling]
---

# Capacity Scheduling

通过Kubernetes原生的ResourceQuota方式进行固定资源分配，集群的整体资源利用率不高。阿里云借鉴Yarn Capacity Scheduling的设计思路，基于Scheduling Framework的扩展机制，在调度侧通过引入弹性配额组实现了Capacity Scheduling功能，在确保用户资源分配的基础上通过资源共享的方式来提升集群的整体资源利用率。本文介绍如何使用Capacity Scheduling功能。

-   [创建ACK Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。
-   集群的Kubernetes版本不低于1.20。

当集群中有多个用户时，为了保障用户有足够的资源使用，管理员会将集群的资源固定分配给不同的用户。传统的方法是通过Kubernetes原生的ResourceQuota方式进行固定资源的分配。因为不同的用户使用资源的周期和方式不同，可能会出现在某一时刻，一些用户的资源不够用，而另一些用户的资源闲置。如果出现多个类似的情况，则整个集群会有很多资源浪费，导致集群的整体资源利用率下降。

## Capacity Scheduling核心功能

针对上述的问题，阿里云基于Scheduling Framework的扩展机制，在调度侧实现了Capacity Scheduling的功能，在确保用户的资源分配的基础上通过资源共享的方式来提升整体资源的利用率。Capacity Scheduling有以下功能：

-   支持定义不同层级的资源配额。如下图所示，您可以根据具体情况（比如：公司的组织结构）配置多个层级的弹性配额。弹性配额组的叶子节点可以对应多个Namespace，但同一个Namespace只能归属于一个叶子节点。

    ![37](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3197249161/p268242.png)

-   支持不同弹性配额之间的资源借用和回收。

    -   Min：您可以使用的保障资源（Guaranteed Resource）。当整个集群资源紧张时，所有用户使用的Min总和需要小于集群的总资源量。
    -   Max：您可以使用的资源上限。

        ![39](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4197249161/p268433.png)

    **说明：**

    -   您的工作负载可以向其他用户借用其未使用的资源额度，但借用后可使用的资源总量依然不能超过Max。
    -   您未使用的Min资源配额可以被其他用户借用，但当该用户需要使用资源时，可以通过抢占的方式回收借出的资源。
-   支持多种资源的配置。您除了可以配置CPU和内存资源，也可以配置如GPU等任何Kubernetes支持的扩展资源（Extended Resource）。

## 使用capacity scheduling功能的使用示例

本文测试集群节点资源为1台ecs.sn2.13xlarge机器（56 vCPU 224 GiB）。

1.  执行以下命令，创建对应的Namespace。

    ```
    kubectl create ns namespace1
    kubectl create ns namespace2
    kubectl create ns namespace3
    kubectl create ns namespace4
    ```

2.  使用以下YAML文件样例，创建相应的弹性配额组。

    ```
    apiVersion: scheduling.sigs.k8s.io/v1beta1
    kind: ElasticQuotaTree
    metadata:
      name: elasticquotatree
      namespace: kube-system # 只有kube-system下才会生效
    spec:
      root:
        name: root # Root节点的Max必须等于Min
        max:
          cpu: 40
          memory: 40Gi
          nvidia.com/gpu: 4
        min:
          cpu: 40
          memory: 40Gi
          nvidia.com/gpu: 4
        children: # 配置第二级
          - name: root.a
            max:
              cpu: 40
              memory: 40Gi
              nvidia.com/gpu: 4
            min:
              cpu: 20
              memory: 20Gi
              nvidia.com/gpu: 2
            children: # 配置第三级
              - name: root.a.1
                namespaces: # 配置对应的Namespace
                  - namespace1
                max:
                  cpu: 20
                  memory: 20Gi
                  nvidia.com/gpu: 2
                min:
                  cpu: 10
                  memory: 10Gi
                  nvidia.com/gpu: 1
              - name: root.a.2
                namespaces: # 配置对应的Namespace
                  - namespace2
                max:
                  cpu: 20
                  memory: 40Gi
                  nvidia.com/gpu: 2
                min:
                  cpu: 10
                  memory: 10Gi
                  nvidia.com/gpu: 1
          - name: root.b
            max:
              cpu: 40
              memory: 40Gi
              nvidia.com/gpu: 4
            min:
              cpu: 20
              memory: 20Gi
              nvidia.com/gpu: 2
            children: # 配置第三级
              - name: root.b.1
                namespaces: # 配置对应的Namespace
                  - namespace3
                max:
                  cpu: 20
                  memory: 20Gi
                  nvidia.com/gpu: 2
                min:
                  cpu: 10
                  memory: 10Gi
                  nvidia.com/gpu: 1
              - name: root.b.2
                namespaces: # 配置对应的Namespace
                  - namespace4
                max:
                  cpu: 20
                  memory: 20Gi
                  nvidia.com/gpu: 2
                min:
                  cpu: 10
                  memory: 10Gi
                  nvidia.com/gpu: 1
    ```

    根据以上YAML文件示例配置，在`namespaces`字段配置对应的命名空间，在`children`字段配置对应子级的弹性配额。配置需要满足以下几点：

    -   同一个弹性配额中的Min≤Max。
    -   子级弹性配额的Min之和≤父级的Min。
    -   Root节点的Min=Max≤集群总资源。
    -   Namespace只与弹性配额的叶子节点有一对多的对应关系，且同一个Namespace只能归属于一个叶子节点。
3.  执行以下命令，查看弹性配额组是否创建成功。

    ```
    kubectl get ElasticQuotaTree
    ```

    预期输出：

    ```
    NAME               AGE
    elasticquotatree   68s
    ```

4.  使用以下YAML文件样例，在`namespace1`中部署服务，Pod的副本数为5个，每个Pod请求CPU资源量为5核。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx1
      namespace: namespace1
      labels:
        app: nginx1
    spec:
      replicas: 5
      selector:
        matchLabels:
          app: nginx1
      template:
        metadata:
          name: nginx1
          labels:
            app: nginx1
        spec:
          containers:
          - name: nginx1
            image: nginx
            resources:
              limits:
                cpu: 5
              requests:
                cpu: 5
    ```

5.  执行以下命令，查看集群Pod的部署情况。

    ```
    kubectl get pods -n namespace1
    ```

    预期输出：

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx1-744b889544-52dbg   1/1     Running   0          70s
    nginx1-744b889544-6l4s9   1/1     Running   0          70s
    nginx1-744b889544-cgzlr   1/1     Running   0          70s
    nginx1-744b889544-w2gr7   1/1     Running   0          70s
    nginx1-744b889544-zr5xz   0/1     Pending   0          70s
    ```

    -   因为当前集群的资源有空闲（`root.max.cpu=40`），当在`namespace1`下的Pod申请CPU资源量超过`root.a.1`设置的`min.cpu=10`时，还可以继续借用其他处于空闲的资源，最多可以申请使用`root.a.1`设置的配额`max.cpu=20`。
    -   当Pod申请CPU资源量超过`max.cpu=20`时，再申请的Pod会处于Pending状态。所以此时申请的5个Pod中，有4个处于Running状态，1个处于Pending状态。
6.  使用以下YAML文件样例，在`namespace2`中部署服务，其中Pod的副本数为5个，每个Pod请求CPU资源量为5核。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx2
      namespace: namespace2
      labels:
        app: nginx2
    spec:
      replicas: 5
      selector:
        matchLabels:
          app: nginx2
      template:
        metadata:
          name: nginx2
          labels:
            app: nginx2
        spec:
          containers:
          - name: nginx2
            image: nginx
            resources:
              limits:
                cpu: 5
              requests:
                cpu: 5
    ```

7.  执行以下命令，查看集群Pod的部署情况。

    ```
    kubectl get pods -n namespace1
    ```

    预期输出：

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx1-744b889544-52dbg   1/1     Running   0          111s
    nginx1-744b889544-6l4s9   1/1     Running   0          111s
    nginx1-744b889544-cgzlr   1/1     Running   0          111s
    nginx1-744b889544-w2gr7   1/1     Running   0          111s
    nginx1-744b889544-zr5xz   0/1     Pending   0          111s
    ```

    ```
    kubectl get pods -n namespace2
    ```

    预期输出：

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx2-556f95449f-4gl8s   1/1     Running   0          111s
    nginx2-556f95449f-crwk4   1/1     Running   0          111s
    nginx2-556f95449f-gg6q2   0/1     Pending   0          111s
    nginx2-556f95449f-pnz5k   1/1     Running   0          111s
    nginx2-556f95449f-vjpmq   1/1     Running   0          111s
    ```

    -   同`nginx1`一样，因为当前集群有资源空闲（`root.max.cpu=40`），当在`namespace2`下Pod申请CPU资源量超过`root.a.2`设置的`min.cpu=10`时，还可以继续借用其他处于空闲的资源，最多可以申请使用`root.a.2`设置的配额`max.cpu=20`。
    -   当Pod申请CPU资源量超过`max.cpu=20`时，再申请的Pod处于Pending状态。所以此时申请的5个Pod中，有4个处于Running状态，1个处于Pending状态。
    -   此时集群中`namespace1`和`namespace2`中的Pod所占用的资源已经为`root`设置的`root.max.cpu=40`。
8.  使用以下YAML文件样例，在`namespace3`中部署服务，其中Pod的副本数为5个，每个Pod请求CPU资源量为5核。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx3
      namespace: namespace3
      labels:
        app: nginx3
    spec:
      replicas: 5
      selector:
        matchLabels:
          app: nginx3
      template:
        metadata:
          name: nginx3
          labels:
            app: nginx3
        spec:
          containers:
          - name: nginx3
            image: nginx
            resources:
              limits:
                cpu: 5
              requests:
                cpu: 5
    ```

9.  执行以下命令，查看集群Pod的部署情况。

    ```
    kubectl get pods -n namespace1
    ```

    预期输出：

    ```
    NAME                      READY   STATUS    RESTARTS   AGE
    nginx1-744b889544-52dbg   1/1     Running   0          6m17s
    nginx1-744b889544-cgzlr   1/1     Running   0          6m17s
    nginx1-744b889544-nknns   0/1     Pending   0          3m45s
    nginx1-744b889544-w2gr7   1/1     Running   0          6m17s
    nginx1-744b889544-zr5xz   0/1     Pending   0          6m17s
    ```

    ```
    kubectl get pods -n namespace2
    ```

    预期输出：

    ```
    NAME                      READY   STATUS    RESTARTS   AGE
    nginx2-556f95449f-crwk4   1/1     Running   0          4m22s
    nginx2-556f95449f-ft42z   1/1     Running   0          4m22s
    nginx2-556f95449f-gg6q2   0/1     Pending   0          4m22s
    nginx2-556f95449f-hfr2g   1/1     Running   0          3m29s
    nginx2-556f95449f-pvgrl   0/1     Pending   0          3m29s
    ```

    ```
    kubectl get pods -n namespace3
    ```

    预期输出：

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx3-578877666-msd7f   1/1     Running   0          4m
    nginx3-578877666-nfdwv   0/1     Pending   0          4m10s
    nginx3-578877666-psszr   0/1     Pending   0          4m11s
    nginx3-578877666-xfsss   1/1     Running   0          4m22s
    nginx3-578877666-xpl2p   0/1     Pending   0          4m10s
    ```

    `nginx3`的弹性配额`root.b.1`的`min`设置为`10`，为了保障其设置的`min`资源，调度器会将`root.a`下之前借用`root.b`的Pod资源归还，使得`nginx3`能够至少得到配额`min.cpu=10`的资源量，保证其运行。

    调度器会综合考虑`root.a`下作业的优先级、可用性以及创建时间等因素，选择相应的Pod归还之前抢占的资源（10核）。因此，`nginx3`得到配额`min.cpu=10`的资源量后，有2个Pod处于Running状态，其他3个仍处于Pending状态。

10. 使用以下YAML文件样例，在`namespace4`中部署服务`nginx4`，其中Pod的副本数为5个，每个Pod请求CPU资源量为5核。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx4
      namespace: namespace4
      labels:
        app: nginx4
    spec:
      replicas: 5
      selector:
        matchLabels:
          app: nginx4
      template:
        metadata:
          name: nginx4
          labels:
            app: nginx4
        spec:
          containers:
          - name: nginx4
            image: nginx
            resources:
              limits:
                cpu: 5
              requests:
                cpu: 5
    ```

11. 执行以下命令，查看集群Pod的部署情况。

    ```
    kubectl get pods -n namespace1
    ```

    预期输出：

    ```
    NAME                      READY   STATUS    RESTARTS   AGE
    nginx1-744b889544-cgzlr   1/1     Running   0          8m20s
    nginx1-744b889544-cwx8l   0/1     Pending   0          55s
    nginx1-744b889544-gjkx2   0/1     Pending   0          55s
    nginx1-744b889544-nknns   0/1     Pending   0          5m48s
    nginx1-744b889544-zr5xz   1/1     Running   0          8m20s
    ```

    ```
    kubectl get pods -n namespace2
    ```

    预期输出：

    ```
    NAME                      READY   STATUS    RESTARTS   AGE
    nginx2-556f95449f-cglpv   0/1     Pending   0          3m45s
    nginx2-556f95449f-crwk4   1/1     Running   0          9m31s
    nginx2-556f95449f-gg6q2   1/1     Running   0          9m31s
    nginx2-556f95449f-pvgrl   0/1     Pending   0          8m38s
    nginx2-556f95449f-zv8wn   0/1     Pending   0          3m45s
    ```

    ```
    kubectl get pods -n namespace3
    ```

    预期输出：

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx3-578877666-msd7f   1/1     Running   0          8m46s
    nginx3-578877666-nfdwv   0/1     Pending   0          8m56s
    nginx3-578877666-psszr   0/1     Pending   0          8m57s
    nginx3-578877666-xfsss   1/1     Running   0          9m8s
    nginx3-578877666-xpl2p   0/1     Pending   0          8m56s
    ```

    ```
    kubectl get pods -n namespace4
    ```

    预期输出：

    ```
    nginx4-754b767f45-g9954   1/1     Running   0          4m32s
    nginx4-754b767f45-j4v7v   0/1     Pending   0          4m32s
    nginx4-754b767f45-jk2t7   0/1     Pending   0          4m32s
    nginx4-754b767f45-nhzpf   0/1     Pending   0          4m32s
    nginx4-754b767f45-tv5jj   1/1     Running   0          4m32s
    ```

    同理，`nginx4`的弹性配额`root.b.2`的`min`设置为`10`，为了保障其设置的`min`资源，调度器会将`root.a`下之前借用`root.b`的Pod资源归还，使得`nginx4`能够至少得到配额`min.cpu=10`的资源量，保证其运行。

    调度器会综合考虑`root.a`下作业的优先级、可用性以及创建时间等因素，选择相应的Pod归还之前抢占的资源（10核）。因此，`nginx4`得到配额`min.cpu=10`资源量后，有2个Pod处于Running状态，其他3个仍处于Pending状态。

    此时，集群所有的弹性配额都使用其`min`设置的保障资源（Guaranteed Resource）。


