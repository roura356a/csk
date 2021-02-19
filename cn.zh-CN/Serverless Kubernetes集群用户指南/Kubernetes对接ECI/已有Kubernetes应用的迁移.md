已有Kubernetes应用的迁移 
======================================

本文介绍如何通过Serverless Kubernetes或者通过Kubernetes扩展虚拟节点的方式来使用ECI，帮助您了解将应用迁移到ECI中需要进行的适配和改造。

迁移前准备工作 
----------------------------

1. 了解Kubernetes与ECI对接的基本原理。更多信息，请参见[Kubernetes对接ECI]()。

   

2. 根据您使用ECI的方式，已经创建了Serverless Kubernetes集群，或者在已有的Kubernetes集群中部署了虚拟节点。

   




管理工具介绍 
---------------------------

您可以通过以下方式管理Kubernetes及ECI实例的运行情况：

* 弹性容器实例控制台

  您可以通过弹性容器实例控制台查看ECI实例的运行情况。操作步骤如下：
  1. 登录[弹性容器实例控制台](https://eci.console.aliyun.com)。

     
  
  2. 在顶部菜单栏左上角处选择地域。

     
  
  3. 在 **容器组** 页面，您可以查看该地域下已经创建的ECI实例。

     
  

  

* 容器服务管理控制台

  您可以通过容器服务管理控制台来操作ASK集群或ACK集群，并查看ECI实例的运行情况。查看ECI实例的操作步骤如下：
  1. 登录[容器服务管理控制台](https://cs.console.aliyun.com/)。

     
  
  2. 在左侧导航栏单击 **集群** 。

     
  
  3. 在集群列表中找到想要查看的集群，单击集群ID进入详情页面。

     
  
  4. 在左侧导航栏，选择 **工作负载\>容器组** 。

     
  
  5. 在 **容器组** 页面，选择命名空间，您可以查看该命名空间下的ECI实例。

     
  

  

* 阿里云CloudShell

  您可以通过阿里云提供的CloudShell来管理Kubernetes集群。具体操作，请参见[在CloudShell上通过kubectl管理Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/在CloudShell上通过kubectl管理Kubernetes集群.md)。
  

* kubectl客户端

  您可以通过kubectl客户端在本地计算机来访问远端的Kubernetes集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。
  




应用迁移限制 
---------------------------

基于公有云的安全性和虚拟节点本身带来的限制，ECI目前还不支持Kubernetes中HostPath、DaemonSet等功能，具体如下表所示。


|        不支持的功能         |          说明           |               推荐替代方案               |
|-----------------------|-----------------------|------------------------------------|
| HostPath              | 挂载本地宿主机文件到容器中         | 使用emptyDir或者NAS文件存储                |
| HostNetwork           | 将宿主机端口映射到容器中          | 使用type=LoadBalancer的负载均衡           |
| DaemonSet             | 在容器所在宿主机上部署Static Pod | 通过sidecar形式在pod中部署多个镜像             |
| Privileged权限          | 容器拥有privileged权限      | 使用Security Context为Pod添加Capability |
| type=NodePort的Service | 通过宿主机端口映射到容器端口        | 使用type=LoadBalancer的负载均衡           |



应用迁移说明 
---------------------------

* Kubernetes集群和Serverless Kubernetes集群共享容器镜像仓库，您可以先将容器镜像上传到容器镜像仓库中，便于镜像拉取。建议您使用专有网络的镜像地址（registry-vpc.xxx）。

  

* Serverless Kubernetes和Kubernetes+虚拟节点扩展支持Deployment，ReplicaSet，Job，Cronjob，StatefulSet等常见controller，可以直接运行。

  

* Serverless Kubernetes和Kubernetes+虚拟节点扩展利用PrivateZone实现服务发现，建议您在创建集群时开启PrivateZone。

  

* Serverless Kubernetes和Kubernetes+虚拟节点扩展支持负载均衡，即配置type=LoadBalancer的Service。更多信息，请参见[访问控制概述](https://help.aliyun.com/document_detail/85979.html?spm=a2c4g.11186623.6.643.53be555cua9npz)。

  以下为yaml示例：

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

  



