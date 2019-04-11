# 子账号Kubernetes应用权限配置指导 {#concept_qlf_lv4_f2b .concept}

本文旨在帮助您了解如何通过容器服务控制台配置子账号对应的Kubernetes RAM集群权限和在集群内相应的Kubernetes RBAC应用权限。

## 配置说明 {#section_ktd_rvn_42b .section}

-   您需要拥有一个阿里云主账号，并有一个或若干个子账号。
-   如果目标子账号涉及RAM读写权限变更，只能由主账号授权。
-   如果某子账号在目标集群或命名空间上被授予预置的管理员角色或自定义中的cluster-admin角色，则该子账号在对应的目标集群或命名空间上可以给其他子账号授权。
-   由于阿里云RAM的安全限制，当您通过容器服务控制台的授权配置涉及到子账号RAM授权的修改时，需要您按照页面上给出的参考策略内容和操作说明，在RAM控制台进行目标子账号的手动授权。

## 公告 {#section_q1y_pv4_f2b .section}

容器服务近期会升级集群授权管理安全策略，禁止所有未授权的子账号访问集群资源，请您及时对管理范围内的集群进行子账号应用权限设置及RAM授权操作。升级后子账号将只拥有授权域内集群的指定访问权限，在授权域外的原有兼容访问模式将被禁止，感谢您的配合。

## 操作步骤 {#section_ot2_4v4_f2b .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在Kubernetes菜单下，单击左侧导航栏中的**集群** \> **授权管理**，进入授权管理页面。
3.  在子账号列表中选择需要授权的子账号，单击**授权**，进入资源授权页面。

    **说明：** 如果您使用子账号授权，请先确保已被授予管理员权限或cluster-admin角色。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/155496331810587_zh-CN.png)

4.  单击**授权管理**页面的加号，添加集群或命名空间级别的权限配置，并选择相应的预置角色；也可以单击配置行首的减号删除目标角色，完成后单击**下一步**。

    **说明：** 当前针对子账号的授权，支持在一个目标集群或命名空间上授予一个预置角色和多个自定义角色。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/155496331810618_zh-CN.png)

    集群和命名空间的预置角色定义可查看下面的角色权限说明：

    | |集群管理权限|应用管理权限|
    |--|------|------|
    |管理员|集群读写权限，可删除、伸缩集群，添加节点|对所有命名空间下资源的读写权限，对集群节点，存储卷，命名空间，配额的读写权限|
    |运维人员|集群读写权限，可删除、伸缩集群，添加节点|对所有命名空间下资源的读写权限，对集群节点，存储卷，命名空间，配额的只读权限|
    |开发人员|集群只读权限|对所有命名空间或所选命名空间下资源的读写权限|
    |受限用户|集群只读权限|对所有命名空间或所选命名空间下资源的只读权限|
    |自定义|集群读写权限，可删除、伸缩集群，添加节点|权限由您所选择的 ClusterRole 决定，请在确定所选 ClusterRole 对各类资源的操作权限后再进行授权，以免子账号获得不符合预期的权限|

5.  在**更新授权策略**页签，如果出现**授权成功**，表示授权完成，操作结束；如果出现**添加授权策略**，请进行如下操作：
    -   如果是子账号授权，请先切换成主账号登录，再进行下一步操作。
    -   如果是主账号授权，请进行下一步操作。
6.  在授权管理页面，单击**复制**后，再单击**策略管理**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/155496331844121_zh-CN.png)

7.  跳转到RAM控制台页面，选择**权限管理** \> **权限策略管理**，单击**新建权限策略**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/155496331844133_zh-CN.png)

8.  进入新建自定义权限策略页面，自定义**策略名称**，配置模式选择**脚本配置**，使用**Ctrl+V**，将步骤6复制的内容粘贴到**策略内容**中，单击**确定**。详细的操作请参见[创建自定义授权策略](intl.zh-CN/用户指南/Kubernetes集群/授权管理/创建自定义授权策略.md#)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/155496331844122_zh-CN.png)

9.  在RAM控制台，选择**人员管理** \> **用户**，在需要授权的用户的右侧，单击**添加权限**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/155496331844124_zh-CN.png)

10. 在弹出的添加权限页面，权限选择**自定义添加权限**，搜索或者在下表中找到自定义的策略名称，单击移动到右侧**已选择**区域。单击**确定**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/155496331844125_zh-CN.png)

11. 配置完成后，您可以使用目标子账号登录容器服务控制台，并进行相关操作。如果您给目标子账号授予的是管理员权限，使用该子账号也可以给其他子账号授权。

## 自定义权限说明 {#section_yrt_rxb_pfb .section}

阿里云容器服务预置了管理员、运维人员、开发人员和受限人员4种标准的访问权限，可满足大部分用户在容器服务控制台上的使用需求。如果您想自由定义集群的访问权限，可使用自定义权限功能。

阿里云容器服务内置了一些自定义权限。

**说明：** 其中cluster-admin权限值得关注，属于集群超级管理员权限，对所有资源都默认拥有权限。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/155496331814375_zh-CN.png)

您可登录到集群Master节点，执行以下命令，查看自定义权限的详情。

`# kubectl get clusterrole`

**说明：** 只有部分clusterrole会在自定义下拉框显示。

```
# kubectl get clusterrole
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

`# kubectl get clusterrole cluster-admin -o yaml`

**说明：** 子账号被授予该集群角色后，在该集群内，可视为与主账号有相同权限的超级账号，拥有操作集群内所有资源的任意权限，建议谨慎授予。

```
# kubectl get clusterrole cluster-admin  -o yaml
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

