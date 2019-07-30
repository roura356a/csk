# 通过负载均衡（Server Load Balancer）访问服务 {#task_1425948 .task}

您可以使用阿里云负载均衡来访问服务。

如果您的集群的cloud-controller-manager版本大于等于v1.9.3，对于指定已有SLB，系统默认不再为该SLB处理监听，用户可以通过设置`service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: "true"`参数来显示启用监听配置，或者手动配置该SLB的监听规则。

执行以下命令，可查看cloud-controller-manager的版本。

``` {#codeblock_agw_mjd_s5c}
root@master # kubectl get po -n kube-system -o yaml|grep image:|grep cloud-con|uniq

  image: registry-vpc.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3
```

## 注意事项 {#section_0iv_2kv_rv0 .section}

-   Cloud Controller Manager（简称CCM）会为`Type=LoadBalancer`类型的Service创建或配置阿里云负载均衡（SLB），包含**SLB**、**监听**、**虚拟服务器组**等资源。
-   对于非LoadBalancer类型的service则不会为其配置负载均衡，这包含如下场景：当用户将一`Type=LoadBalancer`的service变更为`Type!=LoadBalancer`时，CCM也会删除其原先为该Service创建的SLB（用户通过`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id annotation`指定的已有SLB除外）。
-   自动刷新配置：CCM使用声明式API，会在一定条件下自动根据service的配置刷新阿里云负载均衡配置，所有用户自行在SLB控制台上修改的配置均存在被覆盖的风险（使用已有SLB同时不覆盖监听的场景除外），因此不能在SLB控制台手动修改Kubernetes创建并维护的SLB的任何配置，否则有配置丢失的风险。
-   同时支持为serivce指定一个已有的负载均衡，或者让CCM自行创建新的负载均衡。但两种方式在SLB的管理方面存在一些差异：

    指定已有SLB

    -   需要为Service设置`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id` annotation。
    -   SLB配置：此时CCM会使用该SLB做为Service的SLB，并根据其他annotation配置SLB，并且自动的为SLB创建多个虚拟服务器组（当集群节点变化的时候，也会同步更新虚拟服务器组里面的节点）。
    -   监听配置：是否配置监听取决于`service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners:`是否设置为true。 如果设置为false，那么CCM不会为SLB管理任何监听配置。如果设置为true，那么CCM会尝试为SLB更新监听，此时CCM会根据监听名称判断SLB上的监听是否为k8s维护的监听（名字的格式为k8s/Port/ServiceName/Namespace/ClusterID），若Service声明的监听与用户自己管理的监听端口冲突，那么CCM会报错。
    -   SLB的删除： 当Service删除的时候CCM不会删除用户通过id指定的已有SLB。
    CCM管理的SLB

    -   CCM会根据service的配置自动的创建配置**SLB**、**监听**、**虚拟服务器组**等资源，所有资源归CCM管理，因此用户不得手动在SLB控制台更改以上资源的配置，否则CCM在下次Reconcile的时候将配置刷回service所声明的配置，造成非用户预期的结果。
    -   SLB的删除：当Service删除的时候CCM会删除该SLB。
-   后端服务器更新
    -   CCM会自动的为该Service对应的SLB刷新后端虚拟服务器组。当Service对应的后端Endpoint发生变化的时候或者集群节点变化的时候都会自动的更新SLB的后端Server。
    -   `spec.ExternalTraffic = Cluster`模式的Service，CCM默认会将所有节点挂载到SLB的后端（使用BackendLabel标签配置后端的除外）。由于SLB限制了每个ECS上能够attach的SLB的个数（quota），因此这种方式会快速的消耗该quota,当quota耗尽后，会造成Service Reconcile失败。解决的办法，可以使用Local模式的Service。
    -   `spec.ExternalTraffic = Local`模式的Service，CCM默认只会讲Service对应的Pod所在的节点加入到SLB后端。这会明显降低quota的消耗速度。同时支持四层源IP保留。
    -   任何情况下CCM不会将Master节点作为SLB的后端。
    -   CCM会从SLB后端摘除被kubectl drain/cordon的节点。

## 通过命令行操作 {#section_td0_1qf_nnc .section}

方法一：

1.  通过命令行工具创建一个 Nginx 应用。 

    ``` {#codeblock_92a_nwl_hhe}
    root@master # kubectl run nginx --image=registry.aliyuncs.com/acs/netdia:latest
    root@master # kubectl get po 
    NAME                                   READY     STATUS    RESTARTS   AGE
    nginx-2721357637-dvwq3                 1/1       Running   1          6s
    ```

2.  为 Nginx 应用创建阿里云负载均衡服务，指定 `type=LoadBalancer` 来向外网用户暴露 Nginx 服务。 

    ``` {#codeblock_qp1_5hu_2k3}
    root@master # kubectl expose deployment nginx --port=80 --target-port=80 --type=LoadBalancer
    root@master # kubectl get svc
    NAME                  CLUSTER-IP      EXTERNAL-IP      PORT(S)                        AGE
    nginx                 172.19.XX.XX    101.37.XX.XX     80:31891/TCP                   4s
    ```

3.  在浏览器中访问 `http://101.37.XX.XX`，来访问您的 Nginx 服务。

方法二：

1.  将下面的 yml code 保存到 `nginx-svc.yml`文件中。 

    ``` {#codeblock_o3d_fcy_zf9}
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        run: nignx
      name: nginx-01
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

2.  执行如下命令，创建一个 Nginx 应用。 

    ``` {#codeblock_gek_c9f_gdh}
    kubectl apply -f nginx-svc.yml
    ```

3.  执行如下命令，向外网用户暴露 Nginx 服务。 

    ``` {#codeblock_vrc_n9a_8ua}
    root@master # kubectl get service
    NAME           TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE9d
    ngi-01nx       LoadBalancer   172.19.XX.XX    101.37.XX.XX     80:32325/TCP   3h
    ```

4.  在浏览器中访问 `http://101.37.XX.XX`，来访问您的 Nginx 服务。

## 通过 Kubernetes Dashboard 操作 {#section_9pb_73z_j9u .section}

1.  将下面的 yml code 保存到 `nginx-svc.yml`文件中。 

    ``` {#codeblock_e3u_5pt_uw8}
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        run: nginx
      name: http-svc
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

2.  登录[容器服务管理控制台](https://cs.console.aliyun.com)，单击目标集群右侧的**控制台**，进入 Kubernetes Dashboard 页面。
3.  单击**创建**，开始创建应用。 

    ![创建应用](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16677/15644961229066_zh-CN.png)

4.  单击**使用文件创建**。选择刚才保存的nginx-svc.yml 文件。
5.  单击**上传**。 此时，会创建一个阿里云负载均衡实例指向创建的 Nginx 应用，服务的名称为 `http-svc`。
6.  在 Kubernetes Dashboard 上定位到 default 命名空间，选择服务。 可以看到刚刚创建的 `http-svc` 的 Nginx 服务和机器的负载均衡地址 `http://114.55.XX.XX:80`。

    ![访问服务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16677/15644961229067_zh-CN.png)

7.  将该地址拷贝到浏览器中即可访问该服务。

## 通过控制台操作 {#section_jcf_02c_7om .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在Kubernetes菜单下，单击左侧导航栏**应用** \> **无状态**，进入无状态（Deployment）页面。
3.  选择目标集群和命名空间，单击右上角**使用模板创建**。 

    ![创建应用](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16677/156449612313797_zh-CN.png)

4.  **示例模板**选为自定义，将以下内容复制到**模板**中。 

    ``` {#codeblock_z6q_bix_jre}
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        run: nginx
      name: ngnix
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

5.  单击**创建**。
6.  创建成功，单击**Kubernetes 控制台**前往控制台查看创建进度。 

    ![Kubernetes 控制台](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16677/156449612313798_zh-CN.png)

7.  或单击左侧导航栏**路由与负载均衡** \> **服务**，选择目标集群和命名空间，查看已部署的服务。 

    ![部署服务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16677/156449612313800_zh-CN.png)


## 更多信息 {#section_jnq_v5w_nla .section}

阿里云负载均衡还支持丰富的配置参数，包含健康检查、收费类型、负载均衡类型等参数。详细信息参见[负载均衡配置参数表](cn.zh-CN/用户指南/Kubernetes 集群/负载均衡/通过负载均衡（Server Load Balancer）访问服务.md#table_s31_43s_vdb)。

## 注释 {#section_bms_jwb_9x9 .section}

阿里云可以通过注释`annotations`的形式支持丰富的负载均衡功能。

-   使用已有的内网 SLB

    需要指定三个annotation。注意修改成您自己的 Loadbalancer-id。

    **说明：** 

    支持多个Kubernetes Service复用同一个SLB。

    -   Kubernetes通过Service创建的SLB不能复用（会导致SLB被意外删除）。只能复用您手动在控制台（或调用OpenAPI）创建的SLB。
    -   复用同一个SLB的多个Service不能有相同的前端监听端口，否则会造成端口冲突。
    -   复用SLB时，监听的名字以及虚拟服务器组的名字被k8s作为唯一标识符。请勿修改监听和虚拟服务器组的名字。
    -   SLB的名字可以修改。
    -   不支持跨集群复用SLB。
    ``` {#codeblock_eji_hwz_4le}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-address-type: "intranet"
        service.beta.kubernetes.io/alicloud-loadbalancer-id: "your-loadbalancer-id"
        service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: "true" 
      labels:
        run: nginx
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      sessionAffinity: None
      type: LoadBalancer
    ```

-   创建HTTP类型的负载均衡

    ``` {#codeblock_flt_gsc_trc}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port: "http:80"
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

-   创建 HTTPS 类型的负载均衡

    需要先在阿里云控制台上创建一个证书并记录 cert-id，然后使用如下 annotation 创建一个 HTTPS 类型的 SLB。

    ``` {#codeblock_4a3_ub6_czm}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-cert-id: "your-cert-id"
        service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port: "https:443"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      sessionAffinity: None
      type: LoadBalancer
    ```

-   限制负载均衡的带宽

    只限制负载均衡实例下的总带宽，所有监听共享实例的总带宽，参见[共享实例带宽](../../../../cn.zh-CN/监听/共享实例带宽.md#)。

    ``` {#codeblock_oi7_c9f_bau}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-charge-type: "paybybandwidth"
        service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth: "100"
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

-   指定负载均衡规格

    ``` {#codeblock_tss_kri_nxi}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-spec: "slb.s1.small"
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

    默认情况下，使用已有的负载均衡实例，不会覆盖监听，如要强制覆盖已有监听，请配置service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners为true。

    **说明：** 支持多个Kubernetes Service复用同一个SLB。

    -   Kubernetes通过Service创建的SLB不能复用（会导致SLB被意外删除）。只能复用您手动在控制台（或调用OpenAPI）创建的SLB。
    -   复用同一个SLB的多个Service不能有相同的前端监听端口，否则会造成端口冲突。
    -   复用SLB时，监听的名字以及虚拟服务器组的名字被k8s作为唯一标识符。请勿修改监听和虚拟服务器组的名字。
    -   SLB的名字可以修改。
    -   不支持跨集群复用SLB。
    ``` {#codeblock_4od_4zi_kyw}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-id: "your_loadbalancer_id"
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

    强制覆盖已有监听，会删除已有负载均衡实例上的已有监听。

    **说明：** 支持多个Kubernetes Service复用同一个SLB。

    -   Kubernetes通过Service创建的SLB不能复用（会导致SLB被意外删除）。只能复用您手动在控制台（或调用OpenAPI）创建的SLB。
    -   复用同一个SLB的多个Service不能有相同的前端监听端口，否则会造成端口冲突。
    -   复用SLB时，监听的名字以及虚拟服务器组的名字被k8s作为唯一标识符。请勿修改监听和虚拟服务器组的名字。
    -   SLB的名字可以修改。
    -   不支持跨集群复用SLB。
    ``` {#codeblock_l5j_jnv_uqw}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-id: "your_loadbalancer_id"
        service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: "true"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancere
    ```

-   使用指定label的worker节点作为后端服务器

    多个label以逗号分隔。例如："k1:v1,k2:v2"

    多个label之间是`and`的关系。

    ``` {#codeblock_8y5_r8e_4en}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-backend-label: "failure-domain.beta.kubernetes.io/zone:ap-southeast-5a"
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

-   为TCP类型的负载均衡配置会话保持保持时间

    该参数service.beta.kubernetes.io/alicloud-loadbalancer-persistence-tim仅对TCP协议的监听生效。

    如果负载均衡实例配置了多个TCP协议的监听端口，则默认将该配置应用到所有TCP协议的监听端口。

    ``` {#codeblock_qnw_p0g_b1c}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-persistence-timeout: "1800"
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

-   为HTTP&HTTPS协议的负载均衡配置会话保持（insert cookie）

    仅支持HTTP及HTTPS协议的负载均衡实例。

    如果配置了多个HTTP或者HTTPS的监听端口，该会话保持默认应用到所有HTTP和HTTPS监听端口。

    ``` {#codeblock_0jc_lax_szg}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session: "on"
        service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session-type: "insert"
        service.beta.kubernetes.io/alicloud-loadbalancer-cookie-timeout: "1800"
        service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port: "http:80"
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

-   为HTTP&HTTPS协议的负载均衡配置会话保持（server cookie）

    仅支持HTTP及HTTPS协议的负载均衡实例。

    如果配置了多个HTTP或者HTTPS的监听端口，该会话保持默认应用到所有HTTP和HTTPS监听端口。

    ``` {#codeblock_jzr_0q1_vwx}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session: "on"
        service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session-type: "server"
        service.beta.kubernetes.io/alicloud-loadbalancer-cooyour_cookie: "your_cookie"
        service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port: "http:80"
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

-   创建负载均衡时，指定主备可用区

    某些region的负载均衡不支持主备可用区，如ap-southeast-5。

    一旦创建，主备可用区不支持修改。

    ``` {#codeblock_ecv_gdg_7do}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-master-zoneid: "ap-southeast-5a"
        service.beta.kubernetes.io/alicloud-loadbalancer-slave-zoneid: "ap-southeast-5a"
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

-   使用Pod所在的节点作为后端服务器

    ``` {#codeblock_lt6_8re_ujf}
    apiVersion: v1
    kind: Service
    metadata:
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

    **说明：** 注释的内容是区分大小写的。

    |注释|描述|默认值|
    |--|--|---|
    |service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port|多个值之间由逗号分隔，比如：https:443,http:80|无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-address-type|取值可以是internet或者intranet|internet|
    |service.beta.kubernetes.io/alicloud-loadbalancer-slb-network-type|负载均衡的网络类型，取值可以是classic或者vpc|classic|
    |service.beta.kubernetes.io/alicloud-loadbalancer-charge-type|取值可以是paybytraffic或者paybybandwidth|paybytraffic|
    |service.beta.kubernetes.io/alicloud-loadbalancer-id|负载均衡实例的 ID。通过 service.beta.kubernetes.io/alicloud-loadbalancer-id指定您已有的SLB，默认情况下，使用已有的负载均衡实例，不会覆盖监听，如要强制覆盖已有监听，请配置service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners为true。|无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-backend-label|通过 label 指定 SLB 后端挂载哪些worker节点。|无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-spec|负载均衡实例的规格。可参考：[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4182.md\#](../../../../cn.zh-CN/API参考/负载均衡实例/CreateLoadBalancer.md#)|无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-persistence-timeout|会话保持时间。 仅针对TCP协议的监听，取值：0-3600（秒）

 默认情况下，取值为0，会话保持关闭。

 可参考：[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4205.md\#](../../../../cn.zh-CN/API参考/TCP监听/CreateLoadBalancerTCPListener.md#)

 |0|
    |service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session|是否开启会话保持。取值：on | off **说明：** 仅对HTTP和HTTPS协议的监听生效。

 可参考：[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4203.md\#](../../../../cn.zh-CN/API参考/HTTP监听/CreateLoadBalancerHTTPListener.md#)和[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4204.md\#](../../../../cn.zh-CN/API参考/HTTPS监听/CreateLoadBalancerHTTPSListener.md#)

 |off|
    |service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session-type|cookie的处理方式。取值：     -   insert：植入Cookie。
    -   server：重写Cookie。
 **说明：** 

    -   仅对HTTP和HTTPS协议的监听生效。
    -   当service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session取值为on时，该参数必选。
可参考：[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4203.md\#](../../../../cn.zh-CN/API参考/HTTP监听/CreateLoadBalancerHTTPListener.md#)和[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4204.md\#](../../../../cn.zh-CN/API参考/HTTPS监听/CreateLoadBalancerHTTPSListener.md#)

 |无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-cookie-timeout|Cookie超时时间。取值：1-86400（秒） **说明：** 当service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session为on且service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session-type为insert时，该参数必选。

 可参考：[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4203.md\#](../../../../cn.zh-CN/API参考/HTTP监听/CreateLoadBalancerHTTPListener.md#)和[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4204.md\#](../../../../cn.zh-CN/API参考/HTTPS监听/CreateLoadBalancerHTTPSListener.md#)

 |无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-cookie|服务器上配置的Cookie。 长度为1-200个字符，只能包含ASCII英文字母和数字字符，不能包含逗号、分号或空格，也不能以$开头。

 **说明：** 

当service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session为on且service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session-type为server时，该参数必选。

 可参考：[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4203.md\#](../../../../cn.zh-CN/API参考/HTTP监听/CreateLoadBalancerHTTPListener.md#)和[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4204.md\#](../../../../cn.zh-CN/API参考/HTTPS监听/CreateLoadBalancerHTTPSListener.md#)

 |无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-master-zoneid|主后端服务器的可用区ID。|无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-slave-zoneid|备后端服务器的可用区ID。|无|
    |externalTrafficPolicy|哪些节点可以作为后端服务器，取值：     -   Cluster：使用所有后端节点作为后端服务器。

    -   Local：使用Pod所在节点作为后端服务器。

 |Cluster|
    |service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners|绑定已有负载均衡时，是否强制覆盖该SLB的监听。|false：不覆盖|
    |service.beta.kubernetes.io/alicloud-loadbalancer-region|负载均衡所在的地域|无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth|负载均衡的带宽|50|
    |service.beta.kubernetes.io/alicloud-loadbalancer-cert-id|阿里云上的证书 ID。您需要先上传证书|无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-health-check-flag|取值是on | off|默认为off。TCP 不需要改参数。因为 TCP 默认打开健康检查，用户不可设置。|
    |service.beta.kubernetes.io/alicloud-loadbalancer-health-check-type|健康检查类型，取值：tcp | http。 可参考：[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4205.md\#](../../../../cn.zh-CN/API参考/TCP监听/CreateLoadBalancerTCPListener.md#)

 |tcp|
    |service.beta.kubernetes.io/alicloud-loadbalancer-health-check-uri|用于健康检查的URI。 **说明：** 当健康检查类型为TCP模式时，无需配置该参数。

 可参考：[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4205.md\#](../../../../cn.zh-CN/API参考/TCP监听/CreateLoadBalancerTCPListener.md#)

 |无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-port|健康检查使用的端口。取值：     -   -520：默认使用监听配置的后端端口。
    -   1-65535：健康检查的后端服务器的端口。
 可参考：[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4205.md\#](../../../../cn.zh-CN/API参考/TCP监听/CreateLoadBalancerTCPListener.md#)

 |无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-healthy-threshold| 可参考：[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4205.md\#](../../../../cn.zh-CN/API参考/TCP监听/CreateLoadBalancerTCPListener.md#)

 |无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-unhealthy-threshold|健康检查连续成功多少次后，将后端服务器的健康检查状态由fail判定为success。取值： 2-10

 可参考：[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4205.md\#](../../../../cn.zh-CN/API参考/TCP监听/CreateLoadBalancerTCPListener.md#)

 |无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval| 健康检查的时间间隔。

 取值：1-50（秒）

 可参考：[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4205.md\#](../../../../cn.zh-CN/API参考/TCP监听/CreateLoadBalancerTCPListener.md#)

 |无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-timeout| 接收来自运行状况检查的响应需要等待的时间。如果后端ECS在指定的时间内没有正确响应，则判定为健康检查失败。

 取值：1-300（秒）

 **说明：** 如果service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-timeout的值小于service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval的值，则service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-timeout无效，超时时间为service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval的值。

 可参考：[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4205.md\#](../../../../cn.zh-CN/API参考/TCP监听/CreateLoadBalancerTCPListener.md#)

 |无|
    |service.beta.kubernetes.io/alicloud-loadbalancer-health-check-timeout|接收来自运行状况检查的响应需要等待的时间。如果后端ECS在指定的时间内没有正确响应，则判定为健康检查失败。 取值：1-300（秒）

 **说明：** 如果 service.beta.kubernetes.io/alicloud-loadbalancer-health-check-timeout的值小于service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval的值，则 service.beta.kubernetes.io/alicloud-loadbalancer-health-check-timeout无效，超时时间为 service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval的值。

 可参考：[../../../../dita-oss-bucket/SP\_23/DNSLB11870158/ZH-CN\_TP\_4203.md\#](../../../../cn.zh-CN/API参考/HTTP监听/CreateLoadBalancerHTTPListener.md#)

 |无|


