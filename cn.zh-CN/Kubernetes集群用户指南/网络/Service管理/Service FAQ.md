---
keyword: [Service FAQ, SLB, 常见问题]
---

# Service FAQ

本文汇总了使用容器服务ACK Service时的常见问题。

**SLB相关**

-   [为什么看不到Service与LoadBalancer同步过程的事件Event信息？](#section_690_vh0_1lt)
-   [SLB创建一直处于Pending状态如何处理？](#section_og0_e8q_4am)
-   [SLB虚拟服务器组未更新如何处理？](#section_nmr_gvy_po3)
-   [Service注解不生效如何处理？](#section_slc_3sk_7vf)
-   [为何SLB的配置被修改？](#section_g0s_3ac_8p8)
-   [为什么集群内无法访问SLB IP？](#section_bp8_26q_o83)
-   [误删除SLB怎么办？](#section_s2u_6bx_q0b)
-   [删除Service是否会删除SLB？](#section_apr_koj_k8v)
-   [旧版本CCM如何支持SLB重命名？](#section_61e_9ty_sgk)
-   [Local模式下如何自动设置Node权重？](#section_7xa_hpn_yfw)
-   [ACK集群中SLB实例的具体用途](~~141866~~)

**CCM升级相关**

[CCM升级失败如何处理？](#section_xyq_0k8_8ea)

**使用已有SLB常见问题**

-   [为什么复用已有SLB没有生效？](#test)
-   [为什么复用已有SLB时没有配置监听？](#section_a33_ozj_j1k)

**其他**

[Kubernetes的Service如何进行会话保持？](~~149276~~)

## 为什么看不到Service与LoadBalancer同步过程的事件Event信息？

如果执行命令`kubectl -n {your-namespace} describe svc {your-svc-name}`后看不到Event信息的话，请确认您的CCM组件版本是否大于或等于**v1.9.3.276-g372aa98-aliyun**。低于**v1.9.3.276-g372aa98-aliyun**版本的CCM无法看到Service与LoadBalancer同步过程的事件信息。关于如何查看及升级CCM版本的操作，请参见[手动升级CCM组件](/cn.zh-CN/产品公告/【组件升级】升级CCM组件公告.md)。

如果您的CCM组件版本大于或等于**v1.9.3.276-g372aa98-aliyun**，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。

## SLB创建一直处于Pending状态如何处理？

1.  执行命令`kubectl -n {your-namespace} describe svc {your-svc-name}`查看事件信息。

2.  处理事件中的报错信息。关于事件中不同报错信息的处理方式，请参见[报错事件及对应处理方式](#section_kr4_89m_2dh)。

    如果看不到报错信息，请参见[为什么看不到Service与LoadBalancer同步过程的事件Event信息？](#section_690_vh0_1lt)。


## SLB虚拟服务器组未更新如何处理？

1.  执行命令`kubectl -n {your-namespace} describe svc {your-svc-name}`查看事件信息。

2.  处理事件中的报错信息。关于事件中不同报错信息的处理方式，请参见[报错事件及对应处理方式](#section_kr4_89m_2dh)。

    如果看不到报错信息，请参见[为什么看不到Service与LoadBalancer同步过程的事件Event信息？](#section_690_vh0_1lt)。


## Service注解不生效如何处理？

1.  按照以下步骤查看报错信息。

    1.  执行命令`kubectl -n {your-namespace} describe svc {your-svc-name}`查看事件信息。

    2.  处理事件中的报错信息。关于事件中不同报错信息的处理方式，请参见[报错事件及对应处理方式](#section_kr4_89m_2dh)。

2.  如果没有报错信息，分以下三种场景解决：

    -   确认您的CCM版本是否符合对应注解的版本要求。关于注解和CCM的版本对应关系，请参见[常用注解](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/通过Annotation配置负载均衡.md)。
    -   在服务Service列表中，单击目标服务右侧**操作**列下的**查看YAML**，确认服务Service中是否有对应的注解。如果没有对应注解，配置注解信息。

        关于如何配置注解，请参见[通过Annotation配置负载均衡](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/通过Annotation配置负载均衡.md)。

        关于如何查看服务列表，请参见[管理服务](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/管理服务.md)。

    -   确认注解信息是否有误。

## 为何SLB的配置被修改？

CCM使用声明式API，会在一定条件下自动根据Service的配置更新SLB配置。您自行在SLB控制台上修改的配置均存在被覆盖的风险。建议您通过注解的方式配置SLB。关于注解配置方式的更多信息，请参见[通过Annotation配置负载均衡](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/通过Annotation配置负载均衡.md)。

**说明：** 请勿在SLB控制台上手动修改Kubernetes创建并维护的SLB的任何配置，否则有配置丢失的风险，造成Service不可访问。

## 为什么复用已有SLB没有生效？

-   排查CCM版本，低于v1.9.3.105-gfd4e547-aliyun的CCM版本不支持复用。关于如何查看及升级CCM版本的操作，请参见[手动升级CCM组件](/cn.zh-CN/产品公告/【组件升级】升级CCM组件公告.md)。
-   确认复用的SLB是否为集群创建的，不支持复用集群创建的SLB。
-   确认SLB是否为API Server的SLB，不支持复用API Server的SLB。
-   如果是私网SLB，确认SLB是否和集群在同一个VPC中，不支持跨VPC复用SLB。

## 为什么复用已有SLB时没有配置监听？

确认是否在注解中配置了`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners`为`true`。如果没有配置，则不会自动创建监听。

**说明：** 复用已有的负载均衡默认不覆盖已有监听，因为以下两点原因：

-   如果已有负载均衡的监听上绑定了业务，强制覆盖可能会引发业务中断。
-   由于CCM目前支持的后端配置有限，无法处理一些复杂配置。如果有复杂的后端配置需求，可以在不覆盖监听的情况下，通过控制台自行配置监听。

如存在以上两种情况不建议强制覆盖监听，如果已有负载均衡的监听端口不再使用，则可以强制覆盖。

## CCM升级失败如何处理？

关于Cloud Controller Manager（CCM）组件升级失败的解决方案，请参见[Cloud Controller Manager（CCM）组件升级检查失败](https://help.aliyun.com/knowledge_detail/164988.html)。

## 为什么集群内无法访问SLB IP？

关于为何集群内无法访问SLB IP地址的解决方法，请参见[Kubernetes集群中访问LoadBalancer暴露出去的SLB地址不通](https://help.aliyun.com/knowledge_detail/171437.html)。

## 删除Service是否会删除SLB？

如果是复用已有SLB（Service中有`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id: {your-slb-id}`注解），删除Service时不会删除SLB；否则删除Service时会删除对应的SLB。

如果您更改Service类型（例如从LoadBalancer改为NodePort）也会删除对应的SLB。

## 误删除SLB怎么办？

-   **场景一：误删除API Server SLB如何处理？**

    无法恢复，您需要重建集群。具体操作，请参见[创建ACK Pro版集群](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。

-   **场景二：误删除Ingress SLB如何处理？**

    按照以下步骤重建SLB。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在控制台左侧导航栏中，单击**集群**。
    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
    4.  选择**网络** \> **服务**。
    5.  在服务页面顶部的**命名空间**下拉列表中，单击**kube-system**，然后在服务列表中，找到**nginx-ingress-lb**，然后单击**操作**列下的**查看YAML**。

        如果没有**nginx-ingress-lb**，使用以下模板创建名为**nginx-ingress-lb**的Service。

        ```
        apiVersion: v1
        kind: Service
        metadata:
          labels:
            app: nginx-ingress-lb
          name: nginx-ingress-lb
          namespace: kube-system
        spec:
          externalTrafficPolicy: Local
          ports:
          - name: http
            port: 80
            protocol: TCP
            targetPort: 80
          - name: https
            port: 443
            protocol: TCP
            targetPort: 443
          selector:
            app: ingress-nginx
          type: LoadBalancer
        ```

    6.  在编辑YAML对话框中，移除**status**内容，然后单击**更新**以让CCM重建SLB。
-   **场景三：误删除业务相关的SLB如何处理？**
    -   如果不需要SLB对应的Service，删除Service。
    -   如果对应的Service还需要使用，解决方法如下。
        1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
        2.  在控制台左侧导航栏中，单击**集群**。
        3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
        4.  在集群管理页面左侧导航栏选择**网络** \> **服务**。
        5.  在服务页面顶部的**命名空间**下拉框中，单击**所有命名空间**，然后在服务列表中，找到业务Service，然后单击**操作**列下的**查看YAML**。
        6.  在编辑YAML对话框中，移除**status**内容，然后单击**更新**以让CCM重建SLB。

## 旧版本CCM如何支持SLB重命名？

Cloud Controller Manager组件V1.9.3.10后续版本创建的SLB支持自动打TAG从而可以重命名，而V1.9.3.10及之前的版本，您需要手动给该SLB打上一个特定的TAG从而支持SLB重命名。

**说明：**

-   只有Cloud Controller Manager组件V1.9.3.10及之前版本创建的SLB才需要手动打TAG的方式来支持重命名。
-   Service类型为LoadBalancer。

1.  登录到Kubernetes集群Master节点，请参见[通过kubectl工具连接集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

2.  执行`kubectl get svc -n ${namespace} ${service}`命令，查看该Service类型及IP。

    ![service类型](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9395659951/p44550.png)

    **说明：** 您需要将namespace与service替换为所选集群的命名空间及服务名称。

3.  执行以下命令，生成该SLB所需要的Tag。

    ```
    kubectl get svc -n ${namespace} ${service} -o jsonpath="{.metadata.uid}"|awk -F "-" '{print "kubernetes.do.not.delete: "substr("a"$1$2$3$4$5,1,32)}'
    ```

    ![tag](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/1845030361/p44551.png)

4.  登录[负载均衡控制台](https://slb.console.aliyun.com)根据步骤2中所获取的IP，在其所在的地域搜索到该SLB。

5.  根据步骤3生成的Key值和Value值（分别对应上图的1和2），为该SLB打上一个Tag。具体操作，请参见[添加标签](/cn.zh-CN/传统型负载均衡CLB/CLB用户指南/实例/标签/概述.md)。


## Local模式下如何自动设置Node权重？

本文以业务Pod（app=nginx）部署在三台ECS上，通过Service A对外提供服务为例，说明在Local模式下Node权重的计算方式。

![CCM2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9395659951/p103113.png)

-   **V1.9.3.164-g2105d2e-aliyun之前版本**

    如图2所示，在V1.9.3.164-g2105d2e-aliyun之前版本，Local模式的Service其所有后端权重均为100，即所有流量平均分配到这三台ECS上，造成ECS 1上Pod负载较重而ECS 3上的Pod负载较轻，导致Pod负载不均。

    ![CCM3](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9395659951/p103116.png)

-   **V1.9.3.164-g2105d2e-aliyun之后及V1.9.3.276-g372aa98-aliyun之前版本**

    如图所示，在V1.9.3.164-g2105d2e-aliyun之后及V1.9.3.276-g372aa98-aliyun之前版本，CCM会根据Node上部署的Pod数量计算Node权重。经计算三台ECS权重分别为16、33、50，因此流量将大致按照1：2：3的比例分配给三台ECS，Pod负载更加均衡。

    计算公式如下所示：

    ![计算公式](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0495659951/p103112.png)

    ![ccm4](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0495659951/p103117.png)

-   **V1.9.3.276-g372aa98-aliyun及之后版本**

    图3的权重计算方式因为计算精度问题，Pod间还会存在轻微的负载不均。在V1.9.3.276-g372aa98-aliyun及之后版本，CCM将Node上部署的Pod数量设置为Node权重，如图4所示，三台ECS的权重分别为1、2、3，流量会按照1：2：3的比例分配给三台ECS，Pod负载比图3的方式更加均衡。

    计算公式如下所示：

    ![node权重](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0495659951/p118174.png)

    ![node](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0495659951/p118175.png)


## Service报错信息及对应处理方式

不同报错信息的解决方法如下表。

|报错信息|说明及解决方法|
|----|-------|
|`The backend server number has reached to the quota limit of this load balancers`|指SLB虚拟服务组配额不足。

建议您设置SLB外部流量策略为Local模式（设置`externalTrafficPolicy: Local`），因为Cluster模式会导致后端的配额被迅速消耗。如果您设置SLB的外部流量策略为Cluster模式的话，也可以通过标签来指定使用的虚拟服务器。关于如何在注解中添加标签关联后端的虚拟服务器的操作，请参见[通过Annotation配置负载均衡](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/通过Annotation配置负载均衡.md)。 |
|`The backend server number has reached to the quota limit of this load balancer`|SLB后端的虚拟服务器超出额度。多个Service复用一个SLB后端服务器是累加的。建议您创建Service时，新建SLB。|
|`The loadbalancer does not support backend servers of eni type`|共享型SLB不支持ENI。如果SLB后端使用的是ENI，您需要选择性能保障型SLB实例。在Service中添加注解`annotation: service.beta.kubernetes.io/alibaba-cloud-loadbalancer-spec: "slb.s1.small"`。**说明：** 添加注解需要注意是否符合CCM的版本要求。关于注解和CCM的版本对应关系，请参见[常用注解](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/通过Annotation配置负载均衡.md)。 |
|`alicloud: not able to find loadbalancer named [%s] in openapi, but it's defined in service.loaderbalancer.ingress. this may happen when you removed loadbalancerid annotation`|无法根据Service关联SLB。

-   确认该SLB是否还存在，如果不存在且Service无需再使用，则删除对应的Service即可。
-   如果SLB存在，执行以下步骤。
    1.  确认是否是手动在SLB控制台创建的SLB，如果是，则添加复用已有SLB的注解。更多信息，请参见[通过Annotation配置负载均衡](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/通过Annotation配置负载均衡.md)。
    2.  如果是在ACK中自动创建的SLB，确认该SLB是否有标签**kubernetes.do.not.delete**。如果没有该标签，请参见[旧版本CCM如何支持SLB重命名？](#section_61e_9ty_sgk)。 |
|`ORDER.ARREARAGE Message: The account is arrearage.`|服务欠费。|
|`Status Code: 400 Code: Throttlingxxx`|调用太多，触发OpenAPI限流。|
|`Status Code: 400 Code: RspoolVipExist Message: there are vips associating with this vServer group.`|无法删除虚拟服务器组有关联的监听。解决方法：

1.  确认Service中的注解是否带有SLB实例的ID（例如`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id: {your-slb-id}`）。

如果注解带有SLB实例ID，说明是复用的SLB。

2.  在SLB控制台中删除Service中port对应的监听。关于如何删除SLB监听，请参见[管理监听转发规则](/cn.zh-CN/应用型负载均衡ALB/ALB用户指南/监听/管理监听转发规则.md)。 |
|`Status Code: 400 Code: NetworkConflict`|复用内网SLB时，该SLB和集群不在同一个VPC内。请确保您的SLB和集群在同一个VPC内。|
|`Status Code: 400 Code: VSwitchAvailableIpNotExist Message: The specified VSwitch has no available ip.`|虚拟交换机不足，通过`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-vswitch-id: "${YOUR_VSWITCH_ID}"`指定同VPC下另一个虚拟交换机。|
|`PAY.INSUFFICIENT_BALANCE Message: Your account does not have enough balance.`|账号余额少于100元。|
|`The specified Port must be between 1 and 65535.`|ENI模式不支持String类型的`targetPort`，将Service YAML中的`targetPort`字段改为INT类型。|
|`Status Code: 400 Code: ShareSlbHaltSales Message: The share instance has been discontinued.`|共享型SLB不再售卖了，而低版本CCM默认创建共享型SLB。解决方案：升级CCM。 |

**相关文档**  


[Service的负载均衡配置注意事项](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service的负载均衡配置注意事项.md)

[通过Annotation配置负载均衡](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/通过Annotation配置负载均衡.md)

[Cloud Controller Manager](/cn.zh-CN/产品发布记录/组件介绍与变更记录/核心组件/Cloud Controller Manager.md)

