# 旧版本CCM如何支持SLB重命名 {#concept_184803 .concept}

Cloud Controller Manager 组件 v1.9.3.10后续版本创建的SLB支持自动打TAG从而可以重命名，而 v1.9.3.10及之前的版本，您需要手动给该SLB打上一个特定的TAG从而支持SLB重命名。

## 前提条件 {#section_7aw_rn7_oxd .section}

-   只有 Cloud Controller Manager 组件 v1.9.3.10及之前版本创建的SLB才需要手动打TAG的方式来支持重命名。
-   service类型为Loadbalancer。

## 操作步骤 {#section_xsi_yg6_i13 .section}

1.  登录到Kubernetes集群master节点，参见 [通过 kubectl 连接 Kubernetes 集群](../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。
2.  执行`# kubectl get svc -n ${namespace} ${service}`命令，查看该service类型及IP。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/158824/156212258544550_zh-CN.png)

    **说明：** 您需要将$\{namespace\}与$\{service\}替换为所选集群的命名空间及服务名称。

3.  执行以下命令，生成该SLB所需要的TAG。

    `# kubectl get svc -n ${namespace} ${service} -o jsonpath="{.metadata.uid}"|awk -F "-" '{print "kubernetes.do.not.delete: "substr("a"$1$2$3$4$5,1,32)}'`

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/158824/156212258644551_zh-CN.png)

4.  登录[负载均衡控制台](https://slb.console.aliyun.com)[负载均衡控制台](https://partners-intl.console.aliyun.com/#/sls)根据步骤2中所获取的IP，在其所在的region搜索到该SLB。
5.  根据步骤3生成的KEY值和 VALUE值（分别对应上图的1和2），为该SLB打上一个TAG。详情请参见[添加标签](../../../../intl.zh-CN/用户指南/负载均衡实例/管理标签.md#ol_q2w_1vn_vdb)

