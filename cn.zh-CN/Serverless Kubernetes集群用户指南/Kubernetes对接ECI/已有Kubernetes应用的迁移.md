# 已有Kubernetes应用的迁移

本文介绍如何[通过Serverless Kubernetes使用ECI]()或者[通过虚拟节点对接Kubernetes集群]()。希望能帮助您了解在迁移到ECI中需要进行的适配和改造，并帮助您解决部分在迁移应用中可能会遇到的问题。

## 假设

本文假设您对Kubernetes的基本概念已经有所了解，或者已经在私有云或者公有云中使用过基于Kubernetes的容器编排服务。

## 迁移前的准备工作

-   您可以[通过Serverless Kubernetes使用 ECI]()和[通过虚拟节点对接Kubernetes集群]()了解ECI与Kubernetes对接的基本原理。

-   您无需提前创建ECI实例，只需要提前创建好Serverless Kubernetes集群，或者在已有的Kubernetes托管版中部署好Virtual Node Addon。


## 如何管理Kubernetes及ECI运行情况

-   您可以通过[容器服务控制台](https://cs.console.aliyun.com/)来操作Serverless Kubernetes集群和Kubernetes托管版集群。
-   您可以通过阿里云提供的CloudShell来管理Kubernetes集群，详情请参见[在CloudShell上通过kubectl管理Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/在CloudShell上通过kubectl管理Kubernetes集群.md)。
-   您可以通过kubectl客户端在本地计算机来访问远端的Kubernetes集群。详情请参考[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。

## 如何查看已经创建的ECI实例

-   进入[弹性容器实例控制台](https://eci.console.aliyun.com)，在左上角选择相应的可用区，您可以看到已经创建的ECI实例。如果您看到的是空白页面，请申请弹性容器实例页面的访问权限。
-   进入[容器服务控制台](https://cs.console.aliyun.com/)，从左侧导航栏中点击**应用**\>**容器组**，选择对应的集群和namespace，可以查看到已有的pod，被调度到virtual-kubelet节点上的即是ECI实例，点击实例的‘**详情**’可以查看详细信息。

![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/140797/cn_zh/1570607472780/eci-pod.png)

## 应用迁移的限制

virtual-kubelet是作为一个虚拟节点对接Kubernetes，因此ECI实例并不会跑在一个**集中式**的“真实”节点上，而是会被打散分布在整个阿里云的资源池中。

考虑到公有云的安全性和虚拟节点本身带来的限制，ECI目前还不支持Host相关功能以及DaemonSet。具体如下：

|不支持的功能|具体内容|备选方案|
|------|----|----|
|HostPath|Mount本地宿主机文件到容器中|使用emptyDir，或者NAS存储|
|HostNetwork|将宿主机端口映射到容器中|使用type=LoadBalancer的负载均衡|
|DaemonSet|在容器所在宿主机上部署static pod|通过sidecar形式在pod中部署多个镜像|
|Privileged权限|容器拥有privileged权限|使用securityContext为pod添加Capability|
|type=NodePort的Service|通过宿主机端口映射到容器端口|使用type=LoadBalancer的负载均衡|

## 应用迁移的说明

-   Kubernetes集群和Serverless Kubernetes共享容器镜像仓库，因此可以将容器镜像先上传到容器镜像仓库中。为了加速镜像的拉取，建议使用专有网络的镜像地址（registry-vpc.xxx）。
-   Serverless Kubernetes和虚拟节点扩展支持Deployment，ReplicaSet，Job，Cronjob，StatefulSet等常见controller，理论上可以直接运行。
-   Serverless Kubernetes和虚拟节点扩展利用PrivateZone实现服务发现，因此建议在创建集群时默认勾选PrivateZone支持。
-   Serverless Kubernetes和虚拟节点扩展支持type=LoadBalancer 的Service。您可以将Service修改为type=LoadBalancer，详情请参考[访问控制概述](https://help.aliyun.com/document_detail/85979.html?spm=a2c4g.11186623.6.643.53be555cua9npz)。

```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx
  name: nginx
  namespace: default
spec:
  externalTrafficPolicy: Cluster
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
  sessionAffinity: None
  type: LoadBalancer
```

