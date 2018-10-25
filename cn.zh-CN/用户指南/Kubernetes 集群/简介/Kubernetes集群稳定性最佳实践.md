# Kubernetes集群稳定性最佳实践 {#concept_tx4_3ry_nfb .concept}

在生产环境中使用Kubernetes集群时，为了保证稳定可靠的运行在Kubernetes里，下面将介绍构建Kubernetes集群的最佳实践。

## Master节点规格 {#section_g2v_jry_nfb .section}

通过容器服务创建的Kubernetes集群，Master节点上运行着etcd、kube-apiserver、kube-controller等核心组件，对于Kubernetes集群的稳定性有着至关重要的影响，对于生产环境的集群，必须慎重选择Master规格。Master规格跟集群规模有关，集群规模越大，所需要的Master规格也越高。

**说明：** 您可从多个角度衡量"集群规模"：节点数量、Pod数量、部署频率、访问量。

这里简单的认为集群规模就是集群里的节点数量。对于常见的集群规模，可以参考这种如下的方式选择Master节点的规格（对于测试环境，规格可以小一些。下面的选择能尽量保证Master负载维持在一个较低的水平上）：

|节点规模|Master规格|
|----|--------|
|1-5个节点|4C8G\(不建议2C4G\)|
|6-20个节点|4C16G|
|21-100个节点|8C32G|
|100-200个节点|16C64G|

## 选择合理的磁盘大小 {#section_i2v_jry_nfb .section}

Kubernetes节点需要的磁盘空间也不小，Docker镜像、系统日志、应用日志都保存在磁盘上。创建集群的时候，要考虑每个节点上要部署的Pod数量，每个Pod的日志大小、镜像大小、临时数据，再加上一些系统预留的值。

**说明：** Kubernetes集群中，ECS操作系统占用3G的磁盘空间，剩余的磁盘空间由Kubernetes资源对象使用。

|节点类型|磁盘大小|
|----|----|
|Master节点|经典Dedicated Kubernetes集群的系统盘默认大小为120GB，您可进行设置，不低于40GB。|
|Worker节点|经典Dedicated Kubernetes集群的系统盘默认大小为40GB；可选择挂载数据盘，默认大小为100GB，不低于40GB。|

## 使用多可用区 {#section_j2v_jry_nfb .section}

阿里云支持多Region（地域），每个Region下又有不同的可用区。可用区是指在同一地域内，电力和网络互相独立的物理区域。多可用区能够实现跨地域的容灾能力。会带来额外的网络延时。创建Kubernetes集群时，您可选择创建多可用区Kubernetes集群。参见[创建多可用区 Kubernetes 集群](cn.zh-CN/用户指南/Kubernetes 集群/集群管理/创建多可用区 Kubernetes 集群.md#)。

## 声明每个Pod的resource {#section_l2v_jry_nfb .section}

您在使用Kubernetes集群时，经常遇到一个问题：在一个节点上调度了太多的Pod，导致节点负载太高，没法正常对外提供服务。怎么避免这种情况出现呢？

在Kubernetes中部署Pod时，您可以指定这个Pod需要的资源，Kubernetes在部署这个Pod的时候，就会根据Pod的需求找一个具有充足空闲资源的节点部署这个Pod。下面的例子中，声明tomcat这个Pod需要0.25核CPU，64M的内存，运行中实际使用不能超过0.5核CPU和128M内存。

```
apiVersion: v1
kind: Pod
metadata:
  name: tomcat
spec:
  containers:
  - name: tomcat
    image: tomcat
    resources: # 资源声明
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

Kubernetes采用静态资源调度方式，对于每个节点上的剩余资源，它是这样计算的：`节点剩余资源=节点总资源-已经分配出去的资源`，并不是实际使用的资源。如果您自己手动运行一个很耗资源的程序，Kubernetes并不能感知到。

另外所有Pod上都要声明resources。对于没有声明resources的Pod，它被调度到某个节点后，Kubernetes也不会在对应节点上扣掉这个Pod使用的资源。可能会导致节点上调度过去太多的Pod

## 配置监控 {#section_m2v_jry_nfb .section}

阿里云容器服务与云监控进行集成，通过配置节点监控，提供实时的监控服务。通过添加监控告警规则，节点上的资源使用使用量很高的时候，可快速定位问题。

通过容器服务创建Kubernetes集群时，会自动在云监控创建两个应用分组：一个对应Master节点，一个对应Worker节点。我们可以在这两个组下面添加一些报警规则，对组里所有的机器生效。后续加入的节点，也会自动出现在组里，不用单独再去配置报警规则。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24069/154043267814010_zh-CN.png)

主要配置ECS资源的报警规则就可以了。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24069/154043267814012_zh-CN.png)

## 启动时等待下游服务，不要直接退出 {#section_p2v_jry_nfb .section}

有些应用可能会有一些外部依赖，比如需要从数据库（DB）读取数据或者依赖另外一个服务的接口。应用启动的时候，外部依赖未必都能满足。过去手工运维的时候，通常采用依赖不满足立即退出的方式，也就是所谓的failfast，但是在Kubernetes中，这种策略就未必合适了。原因在于Kubernetes中多数运维操作都是自动的，不需要人工介入，比如部署应用，您不用自己选择节点，再到节点上启动应用，应用fail，也不用手动重启，Kubernetes会自动重启应用。负载增高，还可以通过HPA自动扩容。

针对启动时依赖不满足这个场景，假设有两个应用A和B，A依赖B，刚好运行在同一个节点上。这个节点因为某些原因重启了，重启之后，A首先启动，这个时候B还没启动，对A来说就是依赖不满足。如果A还是按照传统的方式直接退出A，当B启动之后，A也不会再启动，必须人工介入处理才行。

Kubernetes的最好的做法是启动时检查依赖，如果不满足，轮询等待，而不是直接退出。可以通过[Init Container](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/#what-can-init-containers-be-used-for)完成这个功能。

## 配置restart policy {#section_q2v_jry_nfb .section}

Pod运行过程中进程退出是个很常见的问题，无论是代码里的一个bug，还是占用内存太多被OOM killer干掉，都会导致应用进程退出，Pod退出。您可在Pod上配置restartPolicy，就能实现Pod挂掉之后自动启动。

```
apiVersion: v1
kind: Pod
metadata:
  name: tomcat
spec:
  containers:
  - name: tomcat
    image: tomcat
    restartPolicy: OnFailure # 
```

restartPolicy有三个可选值

-   Always： 总是自动重启
-   OnFailure：异常退出才自动重启 （进程退出状态非0）
-   Never：永远不重启

## 配置Liveness Probe和Readiness Probe {#section_s2v_jry_nfb .section}

Pod处于Running状态和Pod能正常提供服务是完全不同的概念，一个Running状态的Pod，里面的进程可能发生了死锁而无法提供服务。但是因为Pod还是Running的，Kubernetes也不会自动重启这个Pod。所以我们要在所有Pod上配置Liveness Probe，探测Pod是否真的存活，是否还能提供服务。如果Liveness Probe发现了问题，Kubernetes会重启Pod。

Readiness Probe用于探测Pod是不是可以对外提供服务了。应用启动过程中需要一些时间完成初始化，在这个过程中是没法对外提供服务的，通过Readiness Probe，我们可以告诉Ingress或者Service能不能把流量转发给这个Pod上。当Pod出现问题的时候，Readiness Probe能避免新流量继续转发给这个Pod。

```
apiVersion: v1
kind: Pod
metadata:
  name: tomcat
spec:
  containers:
  - name: tomcat
    image: tomcat
    livenessProbe:
      httpGet:
        path: /index.jsp
        port: 8080
      initialDelaySeconds: 3
      periodSeconds: 3
    readinessProbe:
      httpGet:
        path: /index.jsp
        port: 8080
```

## 每个进程一个容器 {#section_t2v_jry_nfb .section}

很多刚刚接触容器的人喜欢按照旧习惯把容器当作虚拟机（VM）使用，在一个容器里塞入多个进程，监控进程、日志进程、sshd进程、甚至整个Systemd。这种方式有什么问题呢？首先，判断Pod整体的资源占用会变复杂，不方便实施前面提到resource limit。其次，容器内只有一个进程的情况，进程挂了，外面的容器引擎可以清楚的感知到，然后重启容器，如果容器内有多个进程，某个进程挂了，容器未必受影响，外部的容器引擎感知不到容器内进程退出，也不会对容器做任何操作，但是容器实际上已经不能正常工作了。

如果确实有几个进程需要协同工作，在Kubernetes里也很容易实现，举个例子，nginx和php-fpm，通过unix domain socket通信，我们可以用一个包含两个容器的Pod，unix socket放在两个容器的共享volume中。

## 确保不存在SPOF {#section_u2v_jry_nfb .section}

如果应用只有一个实例，当实例失败的时候，虽然Kubernetes能够重启实例，但是中间不可避免地存在一段时间的不可用。甚至更新应用，发布一个新版本的时候，也会出现这种情况。在Kubernetes里，尽量避免直接使用Pod，尽可能使用Deployment/StatefulSet，并且让应用的Pod在两个以上。

