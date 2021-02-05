# 网络管理FAQ

本文主要为您介绍网络管理中的常见问题。

-   [旧版本CCM如何支持SLB重命名](#section_30n_419_ho6)
-   [\#section\_six\_gw0\_14t](#section_six_gw0_14t)
-   [如何手动安装alicloud-application-controller](#section_91h_eqi_dck)
-   [ACK是否支持hostPort的端口映射](#section_ge8_5r0_zcj)
-   [Local模式下如何自动设置Node权重](#section_wet_85s_5fm)

## 旧版本CCM如何支持SLB重命名

Cloud Controller Manager组件V1.9.3.10后续版本创建的SLB支持自动打TAG从而可以重命名，而V1.9.3.10及之前的版本，您需要手动给该SLB打上一个特定的TAG从而支持SLB重命名。 操作步骤如下：

**说明：**

-   只有Cloud Controller Manager组件V1.9.3.10及之前版本创建的SLB才需要手动打TAG的方式来支持重命名。
-   Service类型为Loadbalancer。

1.  登录到Kubernetes集群Master节点，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。

2.  执行`# kubectl get svc -n ${namespace} ${service}`命令，查看该Service类型及IP。

    ![service类型](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9395659951/p44550.png)

    **说明：** 您需要将$\{namespace\}与$\{service\}替换为所选集群的命名空间及服务名称。

3.  执行以下命令，生成该SLB所需要的TAG。

    ```
    kubectl get svc -n ${namespace} ${service} -o jsonpath="{.metadata.uid}"|awk -F "-" '{print "kubernetes.do.not.delete: "substr("a"$1$2$3$4$5,1,32)}'
    ```

    ![tag](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9395659951/p44551.png)

4.  登录[负载均衡控制台](https://slb.console.aliyun.com)[负载均衡控制台](https://partners-intl.console.aliyun.com/#/sls)根据步骤2中所获取的IP，在其所在的地域搜索到该SLB。

5.  根据步骤3生成的KEY值和VALUE值（分别对应上图的1和2），为该SLB打上一个TAG。详情请参见[添加标签](/cn.zh-CN/传统型负载均衡CLB/用户指南/实例/标签/概述.md)。


## 如何手动安装alicloud-application-controller

在阿里云容器服务中，1.10.4及以上版本默认安装alicloud-application-controller，来提供一种基于CRD的分批发布的能力。

**说明：** Kubernetes最新集群已经默认安装alicloud-application-controller，旧版本集群可以通过手动安装的方式部署alicloud-application-controller，要求Kubernetes集群版本不低于为V1.9.3。

您可通过`kubectl create -f alicloud-application-controller.yml`命令部署alicloud-application-controller，在alicloud-application-controller.yml中输入如下的编排模板。

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: alicloud-application-controller
  labels:
    owner: aliyun
    app: alicloud-application-controller
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      owner: aliyun
      app: alicloud-application-controller
  template:
    metadata:
      labels:
        owner: aliyun
        app: alicloud-application-controller
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ''
    spec:
      tolerations:
      - effect: NoSchedule
        operator: Exists
        key: node-role.kubernetes.io/master
      - effect: NoSchedule
        operator: Exists
        key: node.cloudprovider.kubernetes.io/uninitialized
      containers:
        - name: alicloud-application-controller
          image: registry.cn-hangzhou.aliyuncs.com/acs/aliyun-app-lifecycle-manager:0.1-c8d5da8
          imagePullPolicy: IfNotPresent
      serviceAccount: admin
```

## ACK是否支持hostPort的端口映射

-   不支持hostPort。
-   容器服务ACK的Pod地址是可以直接被VPC中其他资源访问的，不需要额外的端口映射。
-   如果需要把服务暴露到外部，可以使用NodePort或者LoadBalancer类型的Service。

## Local模式下如何自动设置Node权重

本文以业务Pod（app=nginx）部署在三台ECS上，通过Service A对外提供服务为例，说明在Local模式下Node权重的计算方式。

![CCM2](../images/p103113.png "")

-   **V1.9.3.164-g2105d2e-aliyun之前版本**

    如图2所示，在V1.9.3.164-g2105d2e-aliyun之前版本，Local模式的Service其所有后端权重均为100，即所有流量平均分配到这三台ECS上，造成ECS 1上Pod负载较重而ECS 3上的Pod负载较轻，导致Pod负载不均。

    ![CCM3](../images/p103116.png "")

-   **V1.9.3.164-g2105d2e-aliyun之后及V1.9.3.276-g372aa98-aliyun之前版本**

    如图3所示，在V1.9.3.164-g2105d2e-aliyun之后及V1.9.3.276-g372aa98-aliyun之前版本，CCM会根据Node上部署的Pod数量计算Node权重。经计算三台ECS权重分别为16、33、50，因此流量将大致按照1:2:3的比例分配给三台ECS，Pod负载更加均衡。

    计算公式如下。

    ![计算公式](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0495659951/p103112.png)

    ![ccm4](../images/p103117.png "")

-   **V1.9.3.276-g372aa98-aliyun及之后版本**

    图3的权重计算方式因为计算精度问题，Pod间还会存在轻微的负载不均。在V1.9.3.276-g372aa98-aliyun及之后版本，CCM将Node上部署的Pod数量设置为Node权重，如图4所示，三台ECS的权重分别为1、2、3，流量会按照1:2:3的比例分配给三台ECS，Pod负载比图3的方式更加均衡。

    计算公式如下。

    ![node权重](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0495659951/p118174.png)

    ![node](../images/p118175.png "")


