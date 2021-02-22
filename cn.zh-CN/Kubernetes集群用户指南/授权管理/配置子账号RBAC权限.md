---
keyword: [子账号, RBAC权限]
---

# 配置子账号RBAC权限

本文主要介绍如何配置子账号对应的Kubernetes集群内RBAC权限。

## 配置说明

-   默认情况下子账号（非集群创建者）没有集群内任何K8s资源的访问权限。
-   请先确保目标子账号在[RAM管理控制台](https://ram.console.aliyun.com/)中至少已被授予指定集群的RAM只读权限。
-   您可以使用容器服务提供的两个预置角色即管理员（拥有集群内全部K8s资源访问权限）和运维人员来对目标子账号进行授权。
-   对于拥有全局权限的管理员子账号，可以在所有集群维度进行一键授权，后续新创建的集群也会自动绑定已经授权的ClusterRole。
-   当运维管理子账号给其他子账号进行RBAC授权时，控制台会过滤其可以授权的集群和命名空间的资源范围，只有当运维管理子账号有指定集群或命名空间的管理员或cluster-admin角色时，才可以给其他子账号进行RBAC授权。
-   支持对多个目标子账号进行批量授权。
-   由于阿里云RAM的安全限制，当您通过容器服务控制台的授权配置涉及到子账号RAM授权的修改时，需要您按照页面上给出的参考策略内容和操作说明，在RAM控制台进行目标子账号的手动授权。

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**授权管理**。

3.  在授权管理页面中的选择子账号页签列表中选择需要授权的子账号，单击**管理权限**，进入集群RBAC配置页签。

    **说明：** 如果您使用子账号授权，请先确保该子账号已完成集群RBAC配置[自定义RAM授权策略](/cn.zh-CN/Kubernetes集群用户指南/授权管理/自定义RAM授权策略.md)文档中的RAM授权，同时已被授予集群内RBAC管理员权限或cluster-admin角色。

4.  单击**集群RBAC配置**页面的加号，添加集群或命名空间级别的权限配置，并选择相应的预置角色；也可以单击配置行首的减号删除目标角色，完成后单击**下一步**。

    **说明：** 当前针对子账号的授权，支持在一个目标集群或命名空间上授予一个预置角色和多个自定义角色。

    ![角色权限配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0175659951/p10618.png)

    集群和命名空间的预置角色定义可查看下面的角色权限说明：

    |角色|集群内RBAC权限|
    |--|---------|
    |管理员|对所有命名空间下所有资源的读写权限。|
    |运维人员|对所有命名空间下控制台可见Kubernetes资源的读写权限，对集群节点，存储卷，命名空间，配额的只读权限。|
    |开发人员|对所有命名空间或所选命名空间下控制台可见Kubernetes资源的读写权限。|
    |受限用户|对所有命名空间或所选命名空间下控制台可见Kubernetes资源的只读权限。|
    |自定义|权限由您所选择的ClusterRole决定，请在确定所选ClusterRole对各类资源的操作权限后再进行授权，以免子账号获得不符合预期的权限。详情请参见[容器服务自定义 RAM 授权策略](/cn.zh-CN/Kubernetes集群用户指南/授权管理/自定义RAM授权策略.md)。|

5.  在**授权提交**页签，如果出现**授权成功**，表示该子账号之前已被授予RAM权限，此时也已完成RBAC授权，操作结束。如果出现如[图 1](#fig_554_7qb_33s)所示，表示该子账号未被授予RAM权限，请按照页面提示，通过RAM控制台对子账号授予指定集群的只读权限。

    1.  在授权提交页面，单击**复制**后，再单击**策略管理**。

        ![授权管理](../images/p44121.png "授权管理")

    2.  跳转到RAM控制台页面，选择**权限管理** \> **权限策略管理**，单击**创建权限策略**。

        ![新建权限策略](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1175659951/p44133.png)

    3.  进入新建自定义权限策略页面，自定义**策略名称**，配置模式选择**脚本配置**，使用**Ctrl+V**，将步骤5.a复制的内容粘贴到**策略内容**中，单击**确定**。详细的操作请参见[t16637.md\#](/cn.zh-CN/Kubernetes集群用户指南/授权管理/自定义RAM授权策略.md)。

        ![自定义权限策略](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1175659951/p44122.png)

    4.  选择**人员管理** \> **用户**，在需要授权的用户的右侧，单击**添加权限**。

        ![添加权限](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1175659951/p44124.png)

    5.  在弹出的添加权限页面，设置**授权范围**，设置权限为**自定义策略**，搜索或者在下表中找到自定义的策略名称，单击策略名称，移动到右侧**已选择**区域。单击**确定**，完成对子账号在指定集群的只读能力的授权。

        ![授权](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1175659951/p44125.png)

    6.  此时，返回容器服务控制台，在授权提交页面，单击**授权提交**，完成子账号RBAC的授权。

6.  配置完成后，您可以使用目标子账号登录容器服务控制台，并进行相关操作。


## 自定义权限说明

阿里云容器服务预置了管理员、运维人员、开发人员和受限用户4种标准的访问权限，可满足大部分用户在容器服务控制台上的使用需求。如果您想自由定义集群的访问权限，可使用自定义权限功能。

阿里云容器服务内置了一些自定义权限。

**说明：** 其中cluster-admin权限值得关注，属于集群超级管理员权限，对所有资源都默认拥有权限。

![自定义权限](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1175659951/p14375.png)

您可登录到集群节点，执行以下命令，查看自定义权限的详情。

`kubectl get clusterrole`

```
kubectl get clusterrole
NAME                                                                   AGE
admin                                                                  13d
alibaba-log-controller                                                 13d
alicloud-disk-controller-runner                                        13d
cluster-admin                                                          13d
cs:admin                                                               13d
edit                                                                   13d
flannel                                                                13d
kube-state-metrics                                                     22h
node-exporter                                                          22h
prometheus-k8s                                                         22h
prometheus-operator                                                    22h
system:aggregate-to-admin                                              13d
....  
system:volume-scheduler                                                13d
view                                                                   13d
            
```

以超级管理员cluster-admin为例，执行以下命令，查看其权限详情。

`kubectl get clusterrole cluster-admin -o yaml`

**说明：** 子账号被授予该集群角色后，在该集群内，可视为与主账号有相同权限的超级账号，拥有操作集群内所有资源的任意权限，建议谨慎授予。

```
kubectl get clusterrole cluster-admin  -o yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  creationTimestamp: 2018-10-12T08:31:15Z
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
  name: cluster-admin
  resourceVersion: "57"
  selfLink: /apis/rbac.authorization.k8s.io/v1/clusterroles/cluster-admin
  uid: 2f29f9c5-cdf9-11e8-84bf-00163e0b2f97
rules:
- apiGroups:
  - '*'
  resources:
  - '*'
  verbs:
  - '*'
- nonResourceURLs:
  - '*'
  verbs:
  - '*'
```

