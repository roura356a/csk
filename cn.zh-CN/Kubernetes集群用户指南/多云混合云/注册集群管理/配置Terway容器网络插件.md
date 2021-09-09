---
keyword: [容器网络插件, Terway网络]
---

# 配置Terway容器网络插件

混合集群中的容器网络插件包括本地数据中心中运行的容器网络插件和云上计算节点上运行的容器网络插件两部分。本文将介绍如何配置混合集群中的Terway容器网络插件。

-   对于[场景二：本地数据中心容器网络模式为BGP网络](#section_jo1_b9e_q7v)及[场景三：本地数据中心容器网络为Host网络](#section_dpw_bab_n97)，在创建注册集群时需要配置Terway网络的参数。

-   根据您的需要选择是否选中IPvlan。
-   配置Pod虚拟交换机。
-   配置Service CIDR。
具体操作，请参见[t380912.dita\#task\_skz\_qwk\_qfb](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/注册集群管理/创建注册集群并接入本地数据中心集群.md)。

-   已完成创建混合集群中[步骤4](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/注册集群管理/创建混合集群.md)之前的操作。

## 场景一：本地数据中心容器网络模式为覆盖（Overlay）网络

若本地数据中心内的容器网络模式为覆盖网络，则云上计算节点也可以复用此网络模式，只需要保证云上计算节点能够正常拉取容器网络插件守护进程集所使用的容器镜像即可。

例如，常见的覆盖网络模式有：

-   Flannel VXLAN模式。
-   Calico IPIP模式。
-   Cilium VXLAN模式。

## 场景二：本地数据中心容器网络模式为BGP网络

若本地数据中心内的容器网络模式为BGP网络，那么云上计算节点需要使用Terway网络。关于云上云下容器网络互通，请参见[云上边界路由器的BGP配置](云上边界路由器的BGP配置t21437.dita#concept_ljj_4vx_dfb)。

在此场景下，需要保证以下条件：

-   云下容器网络插件（如Calico BGP路由反射模式）的守护进程集不要被调度到云上计算节点。
-   同时也要保证Terway网络插件的守护进程集不要被调度到云下计算节点。

在注册集群中，通过注册集群节点池扩容的计算节点都会添加一个固定的节点标签：`alibabacloud.com/external=true`，可以作为区分云上云下节点的过滤条件。

例如，针对云下Calico网络插件，可以使用`nodeAffinity`设置其不被调度到拥有标签`alibabacloud.com/external=true`的节点上。其他任何运行于云下且不希望被调度到云上的工作负载，都可以使用这种方式设置。执行以下命令更新Calico网络插件。

```
cat <<EOF > calico-ds.pactch
spec:
  template:
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: alibabacloud.com/external
                operator: NotIn
                values:
                - "true"
EOF
kubectl -n kube-system patch ds calico-node -p "$(cat calico-ds.pactch)"
```

云上Terway网络插件默认只被调度到拥有标签`alibabacloud.com/external=true`的节点上。

## 场景三：本地数据中心容器网络为Host网络

由于本地数据中心内的容器网络使用的是Host网络，所以只需保证云上Terway网络插件的守护进程集不被调度到云下即可。云上Terway网络插件默认只调度到拥有节点标签为`alibabacloud.com/external=true`的云上节点。

## 安装和配置Terway网络插件

在[场景二](#section_jo1_b9e_q7v)和[场景三](#section_dpw_bab_n97)下，需要为混合集群的云上节点安装和配置Terway网络插件。

**步骤一：安装Terway插件**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **组件管理**。

5.  在组件管理页面单击**网络**页签，在**terway-eniip**组件区域单击**安装**。

    ![terway-eniip安装](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6109875161/p247541.png)

    您可以使用kubeconfig连接注册集群并查看Terway网络插件守护进程集，在混合集群扩容云上节点之前，它将不会被调度到任何一个云下节点上。

    执行以下命令查看Terway网络。

    ```
    kubectl -nkube-system get ds |grep terway
    ```

    预期输出：

    ```
    terway-eniip   0         0         0       0            0           alibabacloud.com/external=true      16s
    ```


**步骤二：配置Terway插件**

在注册集群中，所有Addon组件都使用自定义的AccessKey来获取云资源的操作权限。

1.  创建RAM账号并为账号授予以下RAM Policy。

    具体操作，请参见[自定义RAM授权策略](/cn.zh-CN/Kubernetes集群用户指南/授权/自定义RAM授权策略.md)。

    ```
    {
        "Version": "1",
        "Statement": [
            {
                "Action": [
                    "ecs:CreateNetworkInterface",
                    "ecs:DescribeNetworkInterfaces",
                    "ecs:AttachNetworkInterface",
                    "ecs:DetachNetworkInterface",
                    "ecs:DeleteNetworkInterface",
                    "ecs:DescribeInstanceAttribute",
                    "ecs:AssignPrivateIpAddresses",
                    "ecs:UnassignPrivateIpAddresses",
                    "ecs:DescribeInstances",
                    "ecs:ModifyNetworkInterfaceAttribute"
                ],
                "Resource": [
                    "*"
                ],
                "Effect": "Allow"
            },
            {
                "Action": [
                    "vpc:DescribeVSwitches"
                ],
                "Resource": [
                    "*"
                ],
                "Effect": "Allow"
            }
        ]
    }
    ```

2.  执行以下命令编辑ConfigMap eni-config并配置`eni_conf.access_key`和`eni_conf.access_secret`。

    ```
    kubectl -n kube-system edit cm eni-config
    ```

    eni-config的示例如下所示。

    ```
    kind: ConfigMap
    apiVersion: v1
    metadata:  
     name: eni-config  
     namespace: kube-system
    data:  
     eni_conf: |    
      {      
       "version": "1",      
       "max_pool_size": 5,      
       "min_pool_size": 0,      
       "vswitches": {{.PodVswitchId}},      
       "eni_tags": {"ack.aliyun.com":"{{.ClusterID}}"},      
       "service_cidr": "{{.ServiceCIDR}}",      
       "security_group": "{{.SecurityGroupId}}",      
       "access_key": "",      
       "access_secret": "",      
       "vswitch_selection_policy": "ordered"    
      }  
     10-terway.conf: |    
      {      
       "cniVersion": "0.3.0",      
       "name": "terway",      
       "type": "terway"    
      }
    ```


## 开启Terway组件的NetworkPolicy

在注册集群中Terway组件默认关闭NetworkPolicy。若您不需要开启NetworkPolicy，请跳过该步骤。更多信息，请参见[使用网络策略Network Policy](/cn.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/使用网络策略Network Policy.md)。

**说明：** Terway组件的NetworkPolicy功能依赖Calico CRD。若您的集群已经使用了Calico容器网络插件，开启Terway的NetworkPolicy功能时可能导致已有Calico容器网络异常。若有问题，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)咨询。

1.  使用以下示例部署CRD资源。

    ```
    kubectl apply -f - <<EOF ---
    apiVersion: apiextensions.k8s.io/v1beta1
    kind: CustomResourceDefinition
    metadata:
      name: felixconfigurations.crd.projectcalico.org
    spec:
      scope: Cluster
      group: crd.projectcalico.org
      version: v1
      names:
        kind: FelixConfiguration
        plural: felixconfigurations
        singular: felixconfiguration
    ---
    apiVersion: apiextensions.k8s.io/v1beta1
    kind: CustomResourceDefinition
    metadata:
      name: bgpconfigurations.crd.projectcalico.org
    spec:
      scope: Cluster
      group: crd.projectcalico.org
      version: v1
      names:
        kind: BGPConfiguration
        plural: bgpconfigurations
        singular: bgpconfiguration
    ---
    apiVersion: apiextensions.k8s.io/v1beta1
    kind: CustomResourceDefinition
    metadata:
      name: ippools.crd.projectcalico.org
    spec:
      scope: Cluster
      group: crd.projectcalico.org
      version: v1
      names:
        kind: IPPool
        plural: ippools
        singular: ippool
    ---
    apiVersion: apiextensions.k8s.io/v1beta1
    kind: CustomResourceDefinition
    metadata:
      name: hostendpoints.crd.projectcalico.org
    spec:
      scope: Cluster
      group: crd.projectcalico.org
      version: v1
      names:
        kind: HostEndpoint
        plural: hostendpoints
        singular: hostendpoint
    ---
    apiVersion: apiextensions.k8s.io/v1beta1
    kind: CustomResourceDefinition
    metadata:
      name: clusterinformations.crd.projectcalico.org
    spec:
      scope: Cluster
      group: crd.projectcalico.org
      version: v1
      names:
        kind: ClusterInformation
        plural: clusterinformations
        singular: clusterinformation
    ---
    apiVersion: apiextensions.k8s.io/v1beta1
    kind: CustomResourceDefinition
    metadata:
      name: globalnetworkpolicies.crd.projectcalico.org
    spec:
      scope: Cluster
      group: crd.projectcalico.org
      version: v1
      names:
        kind: GlobalNetworkPolicy
        plural: globalnetworkpolicies
        singular: globalnetworkpolicy
    ---
    apiVersion: apiextensions.k8s.io/v1beta1
    kind: CustomResourceDefinition
    metadata:
      name: globalnetworksets.crd.projectcalico.org
    spec:
      scope: Cluster
      group: crd.projectcalico.org
      version: v1
      names:
        kind: GlobalNetworkSet
        plural: globalnetworksets
        singular: globalnetworkset
    ---
    apiVersion: apiextensions.k8s.io/v1beta1
    kind: CustomResourceDefinition
    metadata:
      name: networkpolicies.crd.projectcalico.org
    spec:
      scope: Namespaced
      group: crd.projectcalico.org
      version: v1
      names:
        kind: NetworkPolicy
        plural: networkpolicies
        singular: networkpolicy
    EOF
    ```

2.  执行以下命令编辑ConfigMap eni-config，添加networkpolicy配置。

    ```
    kubectl -n kube-system edit cm eni-config
    ```

    eni-config的示例如下所示：

    ```
    kind: ConfigMap
    apiVersion: v1
    metadata:
      name: eni-config
      namespace: kube-system
    data:
      eni_conf: |
        {
          ...
          ...
        }
      10-terway.conf: |
        {
          ...
          ...
        }
      disable_network_policy: "false"
    ```


