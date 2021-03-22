---
keyword: [Terway, NetworkPolicy, 大规模]
---

# 优化大规模Tewary集群NetworkPolicy的扩展性

在Terway的集群中，可以使用NetworkPolicy来控制Pod之间的访问。当Terway的集群Node数量多了之后（节点大于100），NetworkPolicy的代理会给Kubernetes的管控造成不小的压力，所以在大规模集群中需要对NetworkPolicy做针对大规模集群的优化调整。本文介绍如何优化大规模Tewary集群中NetworkPolicy的性能。

-   集群网络模式为Terway且集群规模大于100个节点以上，详情请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   [通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

Terway使用Calico的Felix作为NetworkPolicy功能的实现。在大规模（节点大于100）的集群中，每个节点上的Felix都从API Server获取规则，这样会给API Server带来不小的压力，所以在大规模集群中可以采用关闭NetworkPolicy的功能或者部署中继组件Typha的方式降低Felix对API Server的压力。

您可以通过两种方式优化大规模集群中NetworkPolicy的性能：

-   部署NetworkPolicy中继。
-   关闭集群的NetworkPolicy能力。

    **说明：** 关闭集群的NetworkPolicy能力会导致NetworkPolicy的功能不可用。


## 部署NetworkPolicy中继

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  升级集群的Terway组件至最新版本，具体步骤请参见[管理组件](/intl.zh-CN/Kubernetes集群用户指南/集群/升级集群/管理组件.md)。

3.  使用以下模板并执行`kubectl apply -f`部署NetworkPolicy中继组件Typha。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: calico-typha
      namespace: kube-system
      labels:
        k8s-app: calico-typha
    spec:
      ports:
        - port: 5473
          protocol: TCP
          targetPort: calico-typha
          name: calico-typha
      selector:
        k8s-app: calico-typha
    
    ---
    
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: calico-typha
      namespace: kube-system
      labels:
        k8s-app: calico-typha
    spec:
      replicas: 3
      revisionHistoryLimit: 2
      selector:
        matchLabels:
          k8s-app: calico-typha
      template:
        metadata:
          labels:
            k8s-app: calico-typha
          annotations:
            cluster-autoscaler.kubernetes.io/safe-to-evict: 'true'
        spec:
          nodeSelector:
            kubernetes.io/os: linux
          hostNetwork: true
          tolerations:
            - operator: Exists
          serviceAccountName: terway
          priorityClassName: system-cluster-critical
          containers:
          - image: registry-vpc.{REGION-ID}.aliyuncs.com/acs/typha:v3.5.8
            name: calico-typha
            ports:
            - containerPort: 5473
              name: calico-typha
              protocol: TCP
            env:
              - name: TYPHA_LOGSEVERITYSCREEN
                value: "info"
              - name: TYPHA_LOGFILEPATH
                value: "none"
              - name: TYPHA_LOGSEVERITYSYS
                value: "none"
              - name: TYPHA_CONNECTIONREBALANCINGMODE
                value: "kubernetes"
              - name: TYPHA_DATASTORETYPE
                value: "kubernetes"
              - name: TYPHA_HEALTHENABLED
                value: "true"
            livenessProbe:
              httpGet:
                path: /liveness
                port: 9098
                host: localhost
              periodSeconds: 30
              initialDelaySeconds: 30
            readinessProbe:
              httpGet:
                path: /readiness
                port: 9098
                host: localhost
              periodSeconds: 10
    
    ---
    
    apiVersion: policy/v1beta1
    kind: PodDisruptionBudget
    metadata:
      name: calico-typha
      namespace: kube-system
      labels:
        k8s-app: calico-typha
    spec:
      maxUnavailable: 1
      selector:
        matchLabels:
          k8s-app: calico-typha
    
    ---
    
    apiVersion: apiextensions.k8s.io/v1beta1
    kind: CustomResourceDefinition
    metadata:
      name: bgppeers.crd.projectcalico.org
    spec:
      scope: Cluster
      group: crd.projectcalico.org
      version: v1
      names:
        kind: BGPPeer
        plural: bgppeers
        singular: bgppeer
    ```

    **说明：**

    -   修改模板中的\{REGION-ID\}为对应的地域。
    -   根据集群规模修改其中的replicas的副本数（一个副本对应200个节点，最小3副本）。
4.  执行以下命令修改Terway的配置项文件eni-config，增加中继配置felix\_relay\_service: calico-typha。

    ```
    kubectl edit cm eni-config -n kube-system
    #在打开的文件中增加以下中继配置，与eni_conf参数对齐。
    felix_relay_service: calico-typha
    ```

5.  执行以下命令重启集群中的Terway。

    ```
    kubectl get pod -n kube-system  | grep terway | awk '{print $1}' | xargs kubectl delete -n kube-system pod
    ```

    预计输出：

    ```
    pod "terway-eniip-8hmz7" deleted
    pod "terway-eniip-dclfn" deleted
    pod "terway-eniip-rmctm" deleted
    ```


## 关闭集群NetworkPolicy

当您不需要NetworkPolicy的功能时，也可以采用直接关闭集群的NetworkPolicy功能，从而降低NetworkPolicy的代理对API Server的压力。

1.  修改Terway的配置项文件eni-config，增加禁用NetworkPolicy的配置disable\_network\_policy: "true"。

    ```
    kubectl edit cm -n kube-system eni-config 
    # 修改（如果有以下key）或者新增：
    disable_network_policy: "true"
    ```

2.  执行以下命令重启集群中的Terway。

    ```
    kubectl get pod -n kube-system  | grep terway | awk '{print $1}' | xargs kubectl delete -n kube-system pod
    ```

    预计输出：

    ```
    pod "terway-eniip-8hmz7" deleted
    pod "terway-eniip-dclfn" deleted
    pod "terway-eniip-rmctm" deleted
    ```


上述操作完成后，NetworkPolicy的代理会使用中继组件而不会再对Kubernetes的API Server造成过大压力。通过观察集群API Server的SLB的监控情况，API Server的负载下降。

