如何给Pod挂载弹性公网IP 
===================================

本文主要为您介绍如何在Serverless Kubernetes或虚拟节点中给Pod挂载EIP。

背景信息
----

阿里云Serverless Kubernetes服务、虚拟节点推出Pod挂载弹性公网IP功能，此功能使某些Serverless容器应用的部署和服务访问变得更加简单和便利。



* 无需创建VPC NAT网关即可让单个Pod访问公网。

  

* 无需创建Service也可让单个Pod暴露公网服务。

  

* 可以更加灵活而且动态的绑定Pod和EIP。

  




前提条件 
-------------------------

* 创建一个[Serverless Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)或在Kubernetes集群创建一个[部署虚拟节点Chart](/intl.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)。

  

* 请确保该集群的安全组已开放相关端口（本示例中需要开放80端口）。

  



**注意**



* 需要升级vk到v1.0.0.7-aliyun的版本支持

  

* 挂载弹性公网IP仅支持创建容器组时设置，更新容器组时添加或修改相关设置无效

  




操作步骤 
-------------------------

您可以通过以下两种方法给Pod挂载弹性公网IP。

方法一：自动分配弹性公网 EIP 
-------------------------------------

1. 登录[容器服务管理控制台](https://cs.console.aliyun.com/)。

   

2. 在 Kubernetes 菜单下，单击左侧导航栏中的 **应用 \> 无状态** ，进入 **无状态** （Deployment）页面。

   

3. 选择所需的集群和命名空间，选择样例模板或自定义，然后单击 **创建** 。

   您可以使用如下 yaml 示例模板创建 Pod。本例中，通过指定`k8s.aliyun.com/eci-with-eip`为 true， Serverless Kubernetes 服务/虚拟节点会自动为此 Pod 分配一个 EIP，并且绑定到 Pod 上。

   ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9498835951/p51458.png)

       apiVersion: v1
       kind: Pod
       metadata:
         name: nginx
         annotations:
           k8s.aliyun.com/eci-with-eip: "true"
         # k8s.aliyun.com/eip-bandwidth: '5' #注意：指定带宽不需要带单位
       spec:
         containers:
         - image: registry-vpc.cn-hangzhou.aliyuncs.com/jovi/nginx:alpine
           name: nginx
           ports:
           - containerPort: 80
             name: http
             protocol: TCP
         restartPolicy: OnFailure

   
   **说明**

   
   * 您也可以通过 Annotation k8s.aliyun.com/eip-bandwidth指定 EIP 的带宽，默认值为5，单位为M。

     
   
   * 您也可以通过Annotation k8s.aliyun.com/eip-common-bandwith-package-id让EIP绑定共享带宽。

     
   
   * 如果您创建的是 Deployment，那么 Deployment 中的每一个 Pod 都将会被挂载不同的 EIP，请谨慎使用此操作。

     
   

   
   

4. 单击左侧导航栏中的 **应用 \> 容器组** ，查看容器的状态。

   ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9498835951/p51460.png)
   

5. 在目标容器组右侧单击 **更多 \> 编辑** ，弹出 **编辑YAML** 文件。

   
   **说明**

   YAML文件中`k8s.aliyun.com/allocated-eipAddress: 47.110.XX.XX`的 IP 地址即为 EIP 的公网访问地址。

   

6. 在浏览器中输入http://ip地址，您可访问 nginx 欢迎页。

   此处的http://ip地址为 YAML 文件中`k8s.aliyun.com/allocated-eipAddress: 47.110.XX.XX`的 IP 地址。

   ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9498835951/p51465.png)
   **说明**

   此方式中 EIP 为动态分配，其生命周期与 Pod 相同，当删除 Pod 时，动态分配的 EIP 也会一并删除。

   

7. 如果需要为ECI指定线路，需设置`k8s.aliyun.com/eip-isp`。

   ISP字段表示线路类型，默认为BGP，更多信息，请参见[AllocateEipAddressPro](/intl.zh-CN/API参考/弹性公网IP/AllocateEipAddressPro.md)。

   示例如下：

       apiVersion: v1
       kind: Pod
       metadata:
         name: nginx
         annotations:
           k8s.aliyun.com/eci-with-eip: "true"
           k8s.aliyun.com/eip-isp: "BGP"
       spec:
         containers:
         - image: registry-vpc.cn-hangzhou.aliyuncs.com/jovi/nginx:alpine
           name: nginx
           ports:
           - containerPort: 80
             name: http
             protocol: TCP
         restartPolicy: OnFailure

   






方法二：指定弹性公网 IP实例 ID 
---------------------------------------

1. 登录 VPC 管理控制台，购买弹性公网 IP。请参考[申请新EIP](/intl.zh-CN/用户指南/申请EIP/申请新EIP.md)。

   ![弹性公网 IP](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3563659951/p47489.png)
   

2. 登录[容器服务管理控制台](https://cs.console.aliyun.com/?spm=a2c4g.11186623.2.24.41ca31005VJoCa)。

   

3. 在 Kubernetes 菜单下，单击左侧导航栏中的 **应用 \> 无状态** ，进入 **无状态（Deployment）** 页面。

   

4. 选择所需的集群和命名空间，选择样例模板或自定义，然后单击 **创建** 。

   您可以使用如下 yaml 示例模板创建 Pod。本例中，通过指定 Pod 的 Annonation `k8s.aliyun.com/eci-eip-instanceid`为 EIP 实例ID。 

   ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9498835951/p47512.png)

       apiVersion: v1
       kind: Pod
       metadata:
         name: nginx
         annotations:
            k8s.aliyun.com/eci-eip-instanceid : " < Eip Instance Id > "
       spec:
         containers:
         - image: registry-vpc.cn-hangzhou.aliyuncs.com/jovi/nginx:alpine
           name: nginx
           ports:
           - containerPort: 80
             name: http
             protocol: TCP
         restartPolicy: OnFailure

   

   
   **说明**

   
   * \< Eip Instance Id \>需要替换成步骤1中获取的 EIP 实例 ID。

     
   
   * 如果同时设置了 **自动分配弹性公网 EIP** 和 **指定弹性公网IP实例ID** ，则手动指定的EIP无效。

     
   

   

   

5. 单击左侧导航栏中的 **应用 \> 容器组** ，查看容器的状态。

   ![容器组](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3563659951/p47513.png)
   

6. 在浏览器中输入`http://ip`地址，您可访问 nginx 欢迎页。

   

   ![nginx 欢迎页](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3563659951/p47613.png)

   **说明**

   此处的http://ip地址为步骤1中申请的EIP的 **IP地址** 。

   



