# ACK发布Kubernetes 1.18版本说明

阿里云容器服务ACK严格遵循社区一致性认证。本文介绍ACK发布Kubernetes 1.18版本所做的变更说明。

## 版本升级说明

ACK针对Kubernetes 1.18.8版本提供了全链路的组件优化和升级。

|核心组件|版本号|升级注意事项|
|----|---|------|
|Kubernetes|1.18.8|Kubernetes 1.18版本弃用部分常用的APIVersion。建议您在升级集群前对本文档中所列举的弃用APIVersion进行相应升级。|
|Docker|19.03.5（containerd 1.2.10）|无|
|etcd|3.4.3|无|
|CoreDNS|1.6.7|无|

## 版本解读

-   **资源变更与弃用**

    Kubernetes 1.18版本中API相关弃用如下：

    -   所有资源的API apps/v1beta1和apps/v1beta2都将弃用，请使用**apps/v1**代替。
    -   Daemonsets/Deployments/Replicasets资源的API extensions/v1beta1将被弃用，请使用**apps/v1**代替。
    -   Networkpolicies资源的API extensions/v1beta1将被弃用，请使用**networking.k8s.io/v1**代替。
    -   Podsecuritypolicies资源的API extensions/v1beta1将被弃用，请使用**policy/v1beta1**代替。
    标识节点地域和区域信息的Label更新至"topology.kubernetes.io/zone"和"topology.kubernetes.io/region"。建议您更新业务负载中对应的配置。

-   **功能增强**
    -   [Server-side Apply](https://kubernetes.io/blog/2020/04/01/kubernetes-1.18-feature-server-side-apply-beta-2/)引入Beta 2版本。您在资源的metadata.managedFields字段中可以看到资源中各个配置项的所属关系。

    -   正式发布的[NodeLocal DNSCache](https://kubernetes.io/docs/tasks/administer-cluster/nodelocaldns/)功能可以帮助您提高集群DNS的可用性和性能。

    -   [Volume Snapshot](https://kubernetes.io/docs/concepts/storage/volume-snapshots/)进入Beta阶段，支持数据卷备份、恢复、定时备份等操作。

## ACK对Kubernetes 1.18.8版本的增强

针对Kubernetes 1.18.8版本，ACK在kubelet中进行了适配：使用RAW格式数据卷的用户可以对集群进行平滑升级，而无需排空节点。

