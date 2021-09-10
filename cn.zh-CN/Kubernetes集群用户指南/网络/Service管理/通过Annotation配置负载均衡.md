---
keyword: [Annotation, 负载均衡]
---

# 通过Annotation配置负载均衡

通过Service YAML文件中的Annotation（注解），可以实现丰富的负载均衡功能。本文从SLB、监听和后端服务器组三种资源维度介绍通过注解可以对SLB进行的常见配置操作。

## 注解使用说明

-   注解的内容是区分大小写。
-   自2019年09月11日起，`annotations`字段`alicloud`更新为`alibaba-cloud`。

    例如：

    更新前：`service.beta.kubernetes.io/alicloud-loadbalancer-id`

    更新后：`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id`

    系统将继续兼容`alicloud`的写法，您无需做任何修改。


## SLB

**SLB的典型操作**

-   创建一个公网类型的负载均衡

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   创建一个私网类型的负载均衡

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type: "intranet"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   指定负载均衡规格

    负载均衡规格可参见[CreateLoadBalancer](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/负载均衡实例/CreateLoadBalancer.md)。通过该参数可以创建指定规格的SLB，或者更新已有SLB的规格。

    **说明：** 如果您通过SLB控制台修改SLB规格，可能会被CCM修改回原规格，请谨慎操作。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-spec: "slb.s1.small"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   使用已有的负载均衡

    -   默认情况下，使用已有的负载均衡实例，不会覆盖监听，如要强制覆盖已有监听，请配置`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners`为true。

        **说明：** 复用已有的负载均衡默认不覆盖已有监听，因为以下两点原因：

        -   如果已有负载均衡的监听上绑定了业务，强制覆盖可能会引发业务中断。
        -   由于CCM目前支持的后端配置有限，无法处理一些复杂配置。如果有复杂的后端配置需求，可以在不覆盖监听的情况下，通过控制台自行配置监听。
        如存在以上两种情况不建议强制覆盖监听，如果已有负载均衡的监听端口不再使用，则可以强制覆盖。

    -   使用已有的负载均衡暂不支持添加额外标签（`annotation: service.beta.kubernetes.io/alibaba-cloud-loadbalancer-additional-resource-tags`）。
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id: "${YOUR_LOADBALACER_ID}"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   使用已有的负载均衡，并强制覆盖已有监听

    强制覆盖已有监听，如果监听端口冲突，则会删除已有监听。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id: "${YOUR_LOADBALACER_ID}"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners: "true"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   创建负载均衡时，指定主备可用区

    -   某些region的负载均衡不支持主备可用区，例如ap-southeast-5。
    -   一旦创建，主备可用区不支持修改。
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-master-zoneid: "ap-southeast-5a"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-slave-zoneid: "ap-southeast-5a"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   创建按带宽付费的负载均衡

    -   仅支持公网类型的负载均衡实例。

        其他限制，请参见[修改公网负载均衡实例的计费方式](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/负载均衡实例/ModifyLoadBalancerInstanceSpec.md)。

    -   以下两项Annotation必选。

        此处提供的是示例值，请根据实际业务需要自行设置。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-charge-type: "paybybandwidth"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-bandwidth: "2"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

    **说明：** service.beta.kubernetes.io/alibaba-cloud-loadbalancer-bandwidth为带宽峰值。

-   为负载均衡指定虚拟交换机

    -   通过阿里云专有网络控制台查询交换机ID，然后使用如下的Annotation为负载均衡实例指定虚拟交换机。
    -   虚拟交换机必须与Kubernetes集群属于同一个VPC。
    -   为负载均衡指定虚拟交换机，以下两项Annotation必选。
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
       service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type: "intranet"
       service.beta.kubernetes.io/alibaba-cloud-loadbalancer-vswitch-id: "${YOUR_VSWITCH_ID}"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   为负载均衡添加额外标签

    多个Tag以逗号分隔，例如`"k1=v1,k2=v2"`。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-additional-resource-tags: "Key1=Value1,Key2=Value2" 
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   创建IPv6类型的负载均衡

    -   集群的kube-proxy代理模式需要是IPVS。
    -   生成的IPv6地址仅可在支持IPv6的环境中访问。
    -   创建后IP类型不可更改。
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-ip-version: "ipv6"
      name: nginx
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      type: LoadBalancer
    ```

-   为负载均衡开启删除保护

    默认开启删除保护。

    **说明：** 对于LoadBalancer类型的Service创建的负载均衡，如果手动在SLB控制台开启了删除保护，仍可通过`kubectl delete svc {your-svc-name}`的方式删除Service关联的负载均衡。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-delete-protection: "on"
      name: nginx
    spec:
      externalTrafficPolicy: Local
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      type: LoadBalancer
    ```

-   为负载均衡开启配置修改保护

    默认开启配置修改保护。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-modification-protection: "ConsoleProtection"
      name: nginx
    spec:
      externalTrafficPolicy: Local
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      type: LoadBalancer
    ```

-   指定负载均衡名称

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-name: "your-svc-name"
      name: nginx
    spec:
      externalTrafficPolicy: Local
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      type: LoadBalancer
    ```

-   指定负载均衡所属的资源组

    在[阿里云资源管理平台](https://resourcemanager.console.aliyun.com/)查询资源组ID，然后使用以下Annotation为负载均衡实例指定资源组。

    **说明：** 资源组ID创建后不可被修改。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-resource-group-id: "rg-xxxx"
      name: nginx
    spec:
      externalTrafficPolicy: Local
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      type: LoadBalancer
    ```


## 监听

**监听的典型操作**

-   为TCP类型的负载均衡配置会话保持时间

    -   参数`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-persistence-timeout`仅对TCP协议的监听生效。
    -   如果负载均衡实例配置了多个TCP协议的监听端口，则默认将该配置应用到所有TCP协议的监听端口。
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-persistence-timeout: "1800"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   为HTTP和HTTPS协议的监听配置会话保持（insert cookie）

    -   仅支持HTTP及HTTPS协议的负载均衡实例。
    -   如果配置了多个HTTP或者HTTPS的监听端口，该会话保持默认应用到所有HTTP和HTTPS监听端口。
    -   配置insert cookie，以下四项Annotation必选。
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session: "on"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session-type: "insert"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cookie-timeout: "1800"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port: "http:80"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   为负载均衡配置访问控制策略组

    -   需要先在阿里云负载均衡控制台上创建一个负载均衡访问控制策略组，然后记录该访问控制策略组ID（acl-id），然后使用如下Annotation创建一个带有访问控制的负载均衡实例。
    -   白名单适合只允许特定IP访问的场景，black黑名单适用于只限制某些特定IP访问的场景。
    -   创建带有访问控制的负载均衡，以下三项Annotation必选。
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status: "on"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-id: "${YOUR_ACL_ID}"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-type: "white"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   为负载均衡指定转发端口

    -   端口转发是指将HTTP端口的请求转发到HTTPS端口上。
    -   设置端口转发需要先在阿里云控制台上创建一个证书并记录cert-id。
    -   如需设置端口转发，以下三项Annotation必选。
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port: "https:443,http:80"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id: "${YOUR_CERT_ID}"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-forward-port: "80:443"
      name: nginx
      namespace: default
    spec:
      ports:
      - name: https
        port: 443
        protocol: TCP
        targetPort: 443
      - name: http
        port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   为负载均衡设置调度算法

    -   rr（默认值）：轮询，按照访问顺序依次将外部请求依序分发到后端服务器。
    -   wrr：加权轮询，权重值越高的后端服务器，被轮询到的次数（概率）也越高。
    -   wlc：加权最小连接数，除了根据每台后端服务器设定的权重值来进行轮询，同时还考虑后端服务器的实际负载（即连接数）。当权重值相同时，当前连接数越小的后端服务器被轮询到的次数（概率）也越高。
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-scheduler: "wlc"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   创建HTTP类型的监听

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port: "http:80"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   创建HTTPS类型的监听

    需要先在阿里云控制台上创建一个证书并记录cert-id，然后使用如下Annotation创建一个HTTPS类型的SLB。

    **说明：** HTTPS请求会在SLB层解密，然后以HTTP请求的形式发送给后端的Pod。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port: "https:443"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id: "${YOUR_CERT_ID}"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   创建带健康检查的监听
    -   设置TCP类型的健康检查

        -   TCP端口默认开启健康检查，且不支持修改，即`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-flag annotation`无效。
        -   设置TCP类型的健康检查，以下所有Annotation必选。
        ```
        apiVersion: v1
        kind: Service
        metadata:
          annotations:
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-type: "tcp"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-connect-timeout: "8"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-healthy-threshold: "4"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-unhealthy-threshold: "4"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval: "3"
          name: nginx
          namespace: default
        spec:
          ports:
          - port: 80
            protocol: TCP
            targetPort: 80
          selector:
            run: nginx
          type: LoadBalancer
        ```

    -   设置HTTP类型的健康检查

        设置HTTP类型的健康检查，以下所有的Annotation必选。

        ```
        apiVersion: v1
        kind: Service
        metadata:
          annotations:
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-flag: "on"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-type: "http"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-uri: "/test/index.html"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-healthy-threshold: "4"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-unhealthy-threshold: "4"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-timeout: "10"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval: "3"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port: "http:80"
          name: nginx
          namespace: default
        spec:
          ports:
          - port: 80
            protocol: TCP
            targetPort: 80
          selector:
            run: nginx
          type: LoadBalancer
        ```

-   为监听设置连接优雅中断。

    仅支持TCP和UDP协议。如需设置连接优雅中断，以下两项Annotation必选。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-connection-drain: "on"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-connection-drain-timeout: "30"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```


## 后端服务器组

**后端服务器的典型操作**

-   使用指定Label的Worker节点作为后端服务器

    多个Label以逗号分隔。例如`"k1=v1,k2=v2"`。多个Label之间是And关系。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-backend-label: "failure-domain.beta.kubernetes.io/zone=ap-southeast-5a"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   使用Pod所在的节点作为后端服务器

    -   默认`externalTrafficPolicy`为Cluster模式，会将集群中所有节点挂载到后端服务器。Local模式仅将Pod所在节点作为后端服务器。

    -   Local模式需要设置调度策略为加权轮询wrr。

        **说明：**

        在v1.9.3.164-g2105d2e-aliyun及之后版本，外部流量策略设置为**Local**模式的服务会自动根据Node上的Pod数量为Node设置权重，权重计算规则请参见[Local模式下如何自动设置Node权重？](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.mdsection_7xa_hpn_yfw)。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-scheduler: "wrr"
      name: nginx
      namespace: default
    spec:
      externalTrafficPolicy: Local
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   移除SLB后端**unschedulable**状态的节点

    -   `kubectl cordon`与`kubectl drain`命令会将节点置为**unschedulable**状态，默认`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend`的取值为off，此时不会将处于**unschedulable**状态的节点从SLB的后端服务器组移除。
    -   若需要从SLB的后端服务器组移除**unschedulable**状态的节点，请将`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend的`的取值设置为on。
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend: "on"
      name: nginx
    spec:
      externalTrafficPolicy: Local
      ports:
      - name: http
        port: 30080
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      type: LoadBalancer
    ```

-   直接将Pod ENI挂载到SLB后端

    支持在Terway网络模式下，通过annotation：`service.beta.kubernetes.io/backend-type："eni"`将Pod直接挂载到SLB后端，提升网络转发性能。

    ```
      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          service.beta.kubernetes.io/backend-type: "eni"
        name: nginx
      spec:
        ports:
        - name: http
          port: 30080
          protocol: TCP
          targetPort: 80
        selector:
          app: nginx
        type: LoadBalancer
    ```

    **说明：** 您也可以手动将`service.beta.kubernetes.io/backend-type："eni"`中的`eni`设置为`ecs`将ECS挂载到SLB后端。


## 常用注解

-   **SLB常用注解**

    |注解|类型|描述|默认值|支持的CCM版本|
    |--|--|--|---|--------|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type|string|取值：internet或者intranet。    -   internet：服务通过公网访问，此为默认值。对应SLB的**地址类型**必须为**公网**。
    -   intranet：服务通过私网访问。对应SLB的**地址类型**必须为**私网**。
|internet|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-charge-type|string|取值：paybytraffic或者paybybandwidth。|paybytraffic|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id|string|负载均衡实例的ID。通过service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id指定您已有的SLB，默认情况下，使用已有的负载均衡实例，不会覆盖监听，如要强制覆盖已有监听，请配置service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners为true。|无|v1.9.3.81-gca19cd4-aliyun及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-spec|string|负载均衡实例的规格。可参见：[CreateLoadBalancer](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/负载均衡实例/CreateLoadBalancer.md)。|无|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-master-zoneid|string|主后端服务器的可用区ID。|无|v1.9.3.10-gfb99107-aliyun及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-slave-zoneid|string|备后端服务器的可用区ID。|无|v1.9.3.10-gfb99107-aliyun及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners|string|绑定已有负载均衡时，是否强制覆盖该SLB的监听。|false：不覆盖|v1.9.3.81-gca19cd4-aliyun及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-bandwidth|string|负载均衡的带宽，仅适用于公网类型的负载均衡。|50|v1.9.3.10-gfb99107-aliyun及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-vswitch-id|string|负载均衡实例所属的VSwitch ID。设置该参数时需同时设置addresstype为intranet。|无|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-additional-resource-tags|string|需要添加的Tag列表，多个标签用逗号分隔。例如：`"k1=v1,k2=v2"`。|无|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-ip-version|string|负载均衡实例的IP版本，取值：ipv4或ipv6。|ipv4|v1.9.3.220-g24b1885-aliyun及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-delete-protection|string|负载均衡删除保护，取值：on或off。|on|v1.9.3.313-g748f81e-aliyun及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-modification-protection|string|负载均衡配置修改保护，取值：ConsoleProtection或NonProtection。|ConsoleProtection|v1.9.3.313-g748f81e-aliyun及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-resource-group-id|string|负载均衡所属资源组ID。|无|v1.9.3.313-g748f81e-aliyun及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-name|string|负载均衡实例名称。|无|v1.9.3.313-g748f81e-aliyun及以上版本|

-   **监听的常用注解**

    |注解|类型|描述|默认值|支持的CCM版本|
    |--|--|--|---|--------|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-scheduler|string|调度算法。取值wrr、wlc或rr。     -   wrr：权重值越高的后端服务器，被轮询到的次数（概率）也越高。
    -   wlc：除了根据每台后端服务器设定的权重值来进行轮询，同时还考虑后端服务器的实际负载（即连接数）。当权重值相同时，当前连接数越小的后端服务器被轮询到的次数（概率）也越高。
    -   rr：默认取值，按照访问顺序依次将外部请求依序分发到后端服务器。
|rr|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port|string|多个值之间由逗号分隔，例如：`https:443,http:80`。|无|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-persistence-timeout|string|会话保持时间。 仅针对TCP协议的监听，取值：0~3600（秒）。

默认情况下，取值为0，会话保持关闭。

可参见：[CreateLoadBalancerTCPListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/TCP监听/CreateLoadBalancerTCPListener.md)。

|0|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session|string|是否开启会话保持。取值：on或off。**说明：** 仅对HTTP和HTTPS协议的监听生效。

可参见：[CreateLoadBalancerHTTPListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/HTTP监听/CreateLoadBalancerHTTPListener.md)和[CreateLoadBalancerHTTPSListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/HTTPS监听/CreateLoadBalancerHTTPSListener.md)。

|off|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session-type|string|cookie的处理方式。取值：     -   insert：植入Cookie。
    -   server：重写Cookie。
**说明：**

    -   仅对HTTP和HTTPS协议的监听生效。
    -   当service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session取值为on时，该参数必选。
可参见：[CreateLoadBalancerHTTPListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/HTTP监听/CreateLoadBalancerHTTPListener.md)和[CreateLoadBalancerHTTPSListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/HTTPS监听/CreateLoadBalancerHTTPSListener.md)。

|无|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cookie-timeout|string|Cookie超时时间。取值：1s~86400s。**说明：** 当service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session为on且service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session-type为insert时，该参数必选。

可参见：[CreateLoadBalancerHTTPListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/HTTP监听/CreateLoadBalancerHTTPListener.md)和[CreateLoadBalancerHTTPSListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/HTTPS监听/CreateLoadBalancerHTTPSListener.md)。

|无|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cookie|string|服务器上配置的Cookie名称。 长度为1~200个字符，只能包含ASCII英文字母和数字字符，不能包含逗号、分号或空格，也不能以$开头。

**说明：**

当service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session为on且service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session-type为server时，该参数必选。

可参见：[CreateLoadBalancerHTTPListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/HTTP监听/CreateLoadBalancerHTTPListener.md)和[CreateLoadBalancerHTTPSListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/HTTPS监听/CreateLoadBalancerHTTPSListener.md)。

|无|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id|string|阿里云上的证书ID。您需要在[SLB控制台](https://slb.console.aliyun.com/slb/cn-shenzhen/certs)先上传证书。|无|v1.9.3.164-g2105d2e-aliyun及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-flag|string|取值：on或off    -   TCP监听默认为on且不可更改。
    -   HTTP监听默认为off。
|默认为off。TCP不需要改参数。因为TCP默认打开健康检查，用户不可设置。|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-type|string|健康检查类型，取值：tcp或http。 可参见：[CreateLoadBalancerTCPListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/TCP监听/CreateLoadBalancerTCPListener.md)。

|tcp|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-uri|string|用于健康检查的URI。 **说明：** 当健康检查类型为TCP模式时，无需配置该参数。

可参见：[CreateLoadBalancerTCPListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/TCP监听/CreateLoadBalancerTCPListener.md)。

|无|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-connect-port|string|健康检查使用的端口。取值：1~65535。|无|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-healthy-threshold|string|健康检查连续成功多少次后，将后端服务器的健康检查状态由fail判定为success。 取值：2~10

可参见：[CreateLoadBalancerTCPListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/TCP监听/CreateLoadBalancerTCPListener.md)。

|3|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-unhealthy-threshold|string|健康检查连续失败多少次后，将后端服务器的健康检查状态由success判定为fail。取值： 2~10

可参见：[CreateLoadBalancerTCPListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/TCP监听/CreateLoadBalancerTCPListener.md)。

|3|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval|string|健康检查的时间间隔。 取值：1s~50s

可参见：[CreateLoadBalancerTCPListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/TCP监听/CreateLoadBalancerTCPListener.md)。

|2|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-connect-timeout|string|接收来自运行状况检查的响应需要等待的时间，适用于TCP模式。如果后端ECS在指定的时间内没有正确响应，则判定为健康检查失败。 取值：1s~300s

**说明：** 如果service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-connect-timeout的值小于service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval的值，则service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-connect-timeout无效，超时时间为service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval的值。

可参见：[CreateLoadBalancerTCPListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/TCP监听/CreateLoadBalancerTCPListener.md)。

|5|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-timeout|string|接收来自运行状况检查的响应需要等待的时间，适用于HTTP模式。如果后端ECS在指定的时间内没有正确响应，则判定为健康检查失败。 取值：1s~300s

**说明：** 如果service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-timeout的值小于service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval的值，则service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-timeout无效，超时时间为service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval的值。

可参见：[CreateLoadBalancerTCPListener](/cn.zh-CN/传统型负载均衡CLB/CLB开发指南/API参考/TCP监听/CreateLoadBalancerTCPListener.md)。

|5|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-domain|string|用于健康检查的域名。     -   $\_ip：后端服务器的私网IP。当指定了IP或该参数未指定时，负载均衡会使用各后端服务器的私网IP当做健康检查使用的域名。
    -   domain：域名长度为1~80，只能包含字母、数字、点号（.）和连字符（-）。
|无|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-httpcode|string|健康检查正常的HTTP状态码，多个状态码用逗号（,）分割。取值：     -   http\_2xx
    -   http\_3xx
    -   http\_4xx
    -   http\_5xx
默认值为http\_2xx。|http\_2xx|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status|string|是否开启访问控制功能。取值：on或off。|off|v1.9.3.164-g2105d2e-aliyun及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-id|string|监听绑定的访问策略组ID。当AclStatus参数的值为on时，该参数必选。|无|v1.9.3.164-g2105d2e-aliyun及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-type|string|访问控制类型。 取值：white或black。

    -   white：仅转发来自所选访问控制策略组中设置的IP地址或地址段的请求，白名单适用于应用只允许特定IP访问的场景。设置白名单存在一定业务风险。一旦设名单，就只有白名单中的IP可以访问负载均衡监听。如果开启了白名单访问，但访问策略组中没有添加任何IP，则负载均衡监听会转发全部请求。
    -   black： 来自所选访问控制策略组中设置的IP地址或地址段的所有请求都不会转发，黑名单适用于应用只限制某些特定IP访问的场景。如果开启了黑名单访问，但访问策略组中没有添加任何IP，则负载均衡监听会转发全部请求。当AclStatus参数的值为on时，该参数必选。
|无|v1.9.3.164-g2105d2e-aliyun及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-forward-port|string|将HTTP请求转发至HTTPS指定端口。取值如`80:443`。|无|v1.9.3.164-g2105d2e-aliyun及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-connection-drain|string|是否开启连接优雅中断，取值：on或off。|无|v2.0.1及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-connection-drain-timeout|string|设置连接优雅中断超时时间。单位：秒。取值：10~900。|无|v2.0.1及以上版本|

-   **后端服务器组的常用注解**

    |注解|类型|描述|默认值|支持的CCM版本|
    |--|--|--|---|--------|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-backend-label|string|通过Label指定SLB后端挂载哪些Worker节点。|无|v1.9.3及以上版本|
    |externalTrafficPolicy|string|哪些节点可以作为后端服务器，取值：     -   Cluster：使用所有后端节点作为后端服务器。
    -   Local：使用Pod所在节点作为后端服务器。
|Cluster|v1.9.3及以上版本|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend|string|从SLB后端移除SchedulingDisabled Node。取值：on或off。|off|v1.9.3.164-g2105d2e-aliyun及以上版本|
    |service.beta.kubernetes.io/backend-type|string|SLB后端服务器类型。取值：

    -   `eni`：将Pod挂载到SLB后端，仅Terway网络模式下生效，可以提高网络转发性能。
    -   `ecs`：将ECS挂载到SLB后端。
|Flannel网络模式：默认值为`ecs`。Terway网络模式：

    -   2020年8月10日之前创建的Terway集群默认值为`ecs`。
    -   2020年8月10日之后创建的Terway集群默认值为`eni`。
|v1.9.3.164-g2105d2e-aliyun及以上版本|


**相关文档**  


[Service注解不生效如何处理？](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.mdsection_slc_3sk_7vf)

[Service FAQ](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)

