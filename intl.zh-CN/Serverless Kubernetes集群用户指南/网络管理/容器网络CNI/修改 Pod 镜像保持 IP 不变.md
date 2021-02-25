修改 Pod 镜像保持 IP 不变 
======================================

当您在`Virtual-Node`节点部署一个`bare pod`应用，在程序迭代过程解决一些bug或者增加一些特性后，制作新镜像，需要更新`bare pod`镜像，同时需要保证Pod的IP不变。您可以参考此文档。

前提条件 
-------------------------

1. 您需要开通弹性容器实例ECI服务、访问控制（RAM）服务。并完成对弹性容器实例ECI服务的RAM角色授权。

2. 在本地安装`kubectl`命令，同时保证`kubectl`可以与您的kubernetes集群交互。可以参见[通过 kubectl 连接 Kubernetes 集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/集群管理/管理和访问集群/通过kubectl连接Kubernetes集群.md)。




通过命令创建Pod 
------------------------------

准备一个简单的Pod：

    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
      namespace: default
    spec:
      nodeName: virtual-kubelet
      containers:
      - image: nginx:1.7.9
        imagePullPolicy: Always
        name: nginx



执行命令创建Pod：

    kubectl create -f nginx.yaml



通过命令查询Pod信息 
--------------------------------

    $ kubectl get pod/nginx -n default -o wide
    NAME    READY   STATUS     RESTARTS   AGE         IP                    NODE                  NOMINATED NODE   READINESS GATES
    nginx     1/1         Running    0                 7m34s      192.168.0.29   virtual-kubelet   <none>                       <none>



通过ECI控制台查询Pod信息 
------------------------------------

![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/141288/cn_zh/1571103244050/eci_detail_1.jpg)

通过命令修改Pod镜像 
--------------------------------

通过`kubectl`修改`bare pod`的镜像或者镜像tag可以采用一下几种方式：

* kubectl patch

* kubectl edit

* kubectl apply




演示将Pod的镜像`nginx:1.7.9`修改为`nginx:1.9.6`。

kubectl apply方式 
------------------------------------

修改容器镜像tag：

    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
      namespace: default
    spec:
      nodeName: virtual-kubelet
      containers:
      - image: nginx:1.9.6
        imagePullPolicy: Always
        name: nginx	



执行kubectl apply：

    kubectl apply -f nginx.yaml		



执行kubectl验证镜像和IP：

    $ kubectl get pod/nginx -n default -o wide
    NAME    READY   STATUS    RESTARTS   AGE       IP                   NODE                  NOMINATED NODE   READINESS GATES
    nginx     1/1         Running   0                 8m27s   192.168.0.29   virtual-kubelet   <none>                       <none>	 



kubectl edit方式 
-----------------------------------

执行edit命令进入交互模式，修改Pod镜像：

    kubectl edit pod/nginx -o yaml		



执行kubectl验证镜像和IP：

    $ kubectl get pod/nginx -n default -o wide
    NAME    READY   STATUS    RESTARTS   AGE       IP                   NODE                 NOMINATED NODE   READINESS GATES
    nginx     1/1         Running   0                 9m11s   192.168.0.29   virtual-kubelet   <none>                     <none>		



kubectl patch方式 
------------------------------------

执行patch命令修改Pod内容器镜像：

    kubectl patch pod nginx -p '{"spec":{"containers":[{"name": "nginx","image": "nginx:1.9.6"}]}}'



执行kubectl验证镜像和IP：

    $ kubectl get pod -n default -o wide
    NAME    READY   STATUS    RESTARTS   AGE       IP                   NODE                 NOMINATED NODE   READINESS GATES
    nginx     1/1         Running   0                 9m43s   192.168.0.29   virtual-kubelet   <none>                     <none>	



通过ECI控制台验证 
-------------------------------

Pod执行完镜像更新操作后，可以去ECI控制台查看`ContainerGroup`详情，确认镜像是否修改成功。

![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/141288/cn_zh/1571103892091/eci_image_2.jpg)
