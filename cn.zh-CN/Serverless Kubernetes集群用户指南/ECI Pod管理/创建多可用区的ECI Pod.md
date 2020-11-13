# 创建多可用区的ECI Pod

当您为了应对突发流量而进行业务的快速水平扩容，或者启动大量实例进行Job任务处理时，可能会遇到可用区对应规格实例库存不足或者指定的交换机IP耗尽等情况，从而导致ECI实例创建失败。使用ASK的多可用区特性可以提高ECI实例的创建成功率。

-   ECI会把创建Pod的请求分散到所有的vSwitch中，从而达到分散压力的效果。
-   如果创建Pod请求在某一个vSwitch中遇到没有库存的情况，会自动切换到下一个vSwitch继续尝试创建。

## 使用方式

1.  创建ASK集群时选择已有专有网络，并配置多个不同可用区的vSwitch。创建集群请参见[创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。

    ![多可用区ASK](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3548649951/p103604.png)

2.  修改集群可用区配置。

    当集群创建成功后，需要修改集群的vSwitch配置。您无需重建集群，执行以下命令，根据实际需求修改`kube-system/eci-profile configmap`中的`vswitch`字段，修改后即时生效。

    ```
    kubectl -n kube-system edit cm eci-profile
    ```

    ```
    apiVersion: v1
    data:
      kube-proxy: "true"
      privatezone: "true"
      quota-cpu: "192000"
      quota-memory: 640Ti
      quota-pods: "4000"
      region: cn-hangzhou
      resourcegroup: ""
      securitygroup: sg-xxx
      vpc: vpc-xxx
      vswitch: vsw-xxx,vsw-yyy,vsw-zzz
    kind: ConfigMap
    ```


## 常见问题

-   如何在ASK集群中加入其他Node？

    ASK集群没有Node节点的概念。如果您需要让ECI可以部署到其他可用区，可以在kube-system下名为eci-profile的configmap中，追加一个不同可用区的VSW，逗号分隔即可。

-   如何指定Pod部署到不同的可用区？

    部署Pod的时候默认是根据可用区下是否有足够的资源进行随机调度。

    匹配VK，通过三个Annotation实现对多可用区和多规格支持：

    -   - k8s.aliyun.com/eci-schedule-strategy: "VSwitchOrdered"
    -   - k8s.aliyun.com/eci-vswitch: "vsw-11111, vsw-22222"
    -   - k8s.aliyun.com/eci-use-specs: "ecs.c5.4xlarge, ecs.c6.4xlarge,2-4Gi"
    VSwitchOrdered会根据指定的k8s.aliyun.com/eci-vswitch列表顺序创建ECI。通过k8s.aliyun.com/eci-schedule-strategy设置VSwitchRandom或VSwitchOrdered，在创建ECI时实现随机调度到可用区或者按照注解中的vsw顺序来调度。


