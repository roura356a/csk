# 删除 Kubernetes 集群失败：ROS stack 无法删除 {#concept_rz1_lwb_wdb .concept}

## 问题原因 {#section_jkp_5wb_wdb .section}

用户在 ROS 创建的资源下手动添加了一些资源（比如在 ROS 创建的 VPC 下手动添加了一个 VSwitch），ROS 是没有权限删除这些资源的。这就会导致 ROS 删除 Kubernetes 资源时无法处理该 VPC，最终导致删除失败。

**Note:** 有关创建 Kubernetes 集群时 ROS 自动创建的资源，参见[创建Kubernetes集群](../../../../intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。

## 解决办法 {#section_zpk_wwb_wdb .section}

1.  集群删除失败时（集群的状态显示**删除失败**），跳转到 [ROS 管理控制台](https://ros.console.aliyun.com/)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15845/15393318829798_zh-CN.png)

2.  选择集群所在的地域，找到集群对应的资源栈 `k8s-for-cs-{cluster-id}`，可以看到其状态为**删除失败**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15845/15393318839799_zh-CN.png)

3.  单击资源栈的名称进入资源栈详情页面，单击左侧导航栏中的**资源**。

    您可以看到哪些资源删除失败了。本示例中负载均衡下的 VSwitch 删除失败。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15845/15393318839800_zh-CN.png)

4.  进入删除失败的资源所在产品的控制台，并找到该资源。

    本示例中，登录 VPC 管理控制台，找到集群所在的 VPC，并在该 VPC 下找到删除失败的 VSwitch。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15845/15393318839801_zh-CN.png)

5.  单击 VSwitch 右侧的**删除** 尝试手动删除。

    本示例中，由于 VSwitch 下还有资源未释放，所以删除失败。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15845/15393318839802_zh-CN.png)

    手动释放该 VSwitch 下的资源，然后再次尝试删除该 VSwitch。

6.  使用类似的方法手动删除 Kubernetes 集群下所有删除失败的资源，然后再次尝试删除 Kubernetes 集群。

