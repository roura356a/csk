# Istio CoreDNS {#concept_185294 .concept}

## CoreDNS 及其Plugin扩展 {#section_kk1_j2f_98i .section}

CoreDNS 是一个 CNCF 下的孵化级项目，它的前身是 SkyDNS，主要目的是构建一个快速灵活的 DNS 服务器，让用户可以通过不同方式访问和使用 DNS 内的数据。基于 Caddy 服务器框架，CoreDNS 实现了一个插件链的架构，将大量逻辑抽象成插件 Plugin 的形式暴露给使用者，每个插件都执行 DNS 功能，例如 Kubernetes 的 DNS 服务发现、Prometheus 监控等。

除了插件化之外，CoreDNS 还有以下特性：

-   配置简单化： 引入表达力更强的 DSL，即 Corefile 形式的配置文件（也是基于 Caddy 框架开发）。
-   一体化的解决方案：区别于 kube-dns，CoreDNS 编译出来就是一个单独的二进制可执行文件，内置了 Cache、Backend Storage、Health Check 等功能，无需第三方组件来辅助实现其他功能，从而使得部署更方便，内存管理更为安全。

## Corefile 介绍 {#section_6as_jce_vso .section}

Corefile 是 CoreDNS 的配置文件（源于 Caddy 框架的配置文件 Caddyfile），定义了以下内容：

-   服务器以什么协议监听在哪个端口（可以同时定义多个 server 监听不同端口）
-   服务器负责哪个 zone 的 authoritative DNS 解析
-   服务器将加载哪些插件

一个典型的 Corefile 格式如下所示。

``` {#codeblock_p6l_8bn_qz4}
ZONE:[PORT] {
    [PLUGIN] ...
}
```

Corefile 的各参数含义如下：

|参数|含义|
|--|--|
|ZONE|用于定义服务器负责的 zone|
|PORT|监听端口，可选项，默认为 53|
|PLUGIN|定义服务器所要加载的插件plugin，每个 plugin 可以有多个参数|

## Plugin工作机制 {#section_xoa_ct2_02k .section}

当 CoreDNS 启动后，它将根据配置文件启动不同 server ，每台 server 都拥有自己的插件链。当有 DNS 请求时，它将依次经历如下 3 步逻辑：

-   如果有当前请求的 server 有多个 zone，将采用贪心原则选择最匹配的 zone。
-   一旦找到匹配的 server，按照 plugin.cfg 定义的顺序执行插件链上的插件。
-   每个插件将判断当前请求是否应该处理，将有以下几种可能：
    -   请求被当前插件处理

        插件将生成对应的响应并回给客户端，此时请求结束，下一个插件将不会被调用，如 whoami 插件。

    -   请求不被当前插件处理

        直接调用下一个插件。如果最后一个插件执行错误，服务器返回 SERVFAIL 响应。

    -   请求被当前插件以 Fallthrough 形式处理

        如果请求在该插件处理过程中有可能将跳转至下一个插件，该过程称为 fallthrough，并以关键字 fallthrough 来决定是否允许此项操作，例如 host 插件，当查询域名未位于/etc/hosts，则调用下一个插件。

        -   请求在处理过程被携带 Hint。
        -   请求被插件处理，并在其响应中添加了某些信息（hint）后继续交由下一个插件处理。这些额外的信息将组成对客户端的最终响应，如 metric 插件。

## CoreDNS 与 Kubernetes {#section_4c6_bmg_712 .section}

从 Kubernetes 1.11开始，CoreDNS 作为 Kubernetes 的 DNS 插件进入GA状态，Kubernetes 推荐使用 CoreDNS 作为集群内的 DNS 服务。在阿里云容器服务 Kubernetes 1.11.5 及更新版本中默认安装使用 CoreDNS 作为 DNS 服务。

执行`kubectl -n kube-system get configmap coredns -oyaml`命令可以查看 CoreDNS 配置信息。

``` {#codeblock_3qc_708_cid}
apiVersion: v1
data:
  Corefile: |-
    .:53 {
        errors
        health
        kubernetes cluster.local in-addr.arpa ip6.arpa {
           pods insecure
           upstream
           fallthrough in-addr.arpa ip6.arpa
        }
        prometheus :9153
        proxy . /etc/resolv.conf
        cache 30
        reload
    }
kind: ConfigMap
metadata:
  creationTimestamp: "2018-12-28T07:28:34Z"
  name: coredns
  namespace: kube-system
  resourceVersion: "2453430"
  selfLink: /api/v1/namespaces/kube-system/configmaps/coredns
  uid: 2f3241d5-0a72-11e9-99f1-00163e105bdf
```

配置文件各参数的含义如下：

|名称|含义|
|--|--|
|errors|错误会被记录到标准输出|
|health|健康状况，可以通过http://localhost:8080/health查看|
|kubernetes|根据服务的IP响应DNS查询请求，Cluster Domain默认为cluster.local|
|prometheus|可以通过http://localhost:9153/metrics获取prometheus格式的监控数据|
|proxy|本地无法解析后，向上级地址进行查询，默认使用宿主机的/etc/resolv.conf配置|
|cache|缓存时间|

## 通过应用目录简便部署 Istio CoreDNS {#section_yh5_qjp_vx6 .section}

阿里云容器服务 Kubernetes 1.11.5目前已经上线，可以通过容器服务管理控制台非常方便地快速创建 Kubernetes 集群。具体过程可以参考[创建Kubernetes集群](../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes 集群.md#)。

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/)。
2.  选择**市场** \> **应用目录**，在右侧选中**ack-istio-coredns**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/159221/156404706549799_zh-CN.png)

3.  在应用目录 - ack-istio-coredns中，单击**参数**，可以通过修改参数配置进行定制化，如下所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/159221/156404706549798_zh-CN.png)

    参数描述如下：

    |参数|含义|参数值|默认值|
    |--|--|---|---|
    |replicaCount|指定副本数|number|1|
    |coreDNSImage|指定coredns镜像名称|valid image tag|coredns/coredns:1.2.2|
    |coreDNSPluginImage|指定插件镜像名称|valid image name|registry.cn-hangzhou.aliyuncs.com/aliacs-app-catalog/istio-coredns-plugin:v1.0-istio-1.1|

4.  修改完成后，在右侧的创建页面，在右侧选择对应的集群，同时可以看到命名空间已设定为istio-system，发布名称已设定为istio-coredns，单击**创建**。

    几秒钟后，在弹出Helm 发布列表 - istio-coredns页面，可以看到 Istio CoreDNS 在集群中被创建出来。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/159221/156404706549800_zh-CN.png)


## 更改集群 CoreDNS 配置 {#section_qji_vya_egl .section}

1.  可以通过执行`kubectl get svc istiocoredns -n istio-system`命令，来获取istiocoredns服务的Cluster IP。

    ``` {#codeblock_siy_rhi_p2r}
    NAME           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)         AGE
    istiocoredns   ClusterIP   172.19.XX.XX   <none>         53/UDP,53/TCP   44m
    ```

    更新集群的coredns的配置项configmap， 将此istiocoredns服务作为 \*.global域的上游DNS服务，在此配置项configmap中添加域\*.global，如下所示。

    ``` {#codeblock_3p2_hfu_o6y}
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: coredns
      namespace: kube-system
    data:
      Corefile: |-
        .:53 {
            errors
            health
            kubernetes cluster.local in-addr.arpa ip6.arpa {
               pods insecure
               upstream
               fallthrough in-addr.arpa ip6.arpa
            }
            prometheus :9153
            proxy . /etc/resolv.conf
            cache 30
            reload
        }
        global:53 {
             errors
             cache 30
             proxy . {replace this with the actual cluster IP of this istiocoredns service}
    ```

2.  更改完成后，集群的 CoreDNS 容器会重新加载这些配置内容，可以通过如下命令行查看加载日志。

    ``` {#codeblock_8ei_aoh_nv9}
    # kubectl get -n kube-system pod | grep coredns
    coredns-8645f4b4c6-5frkg                                     1/1     Running   0          20h
    coredns-8645f4b4c6-lj59t                                     1/1     Running   0          20h
    
    # kubectl logs -f -n kube-system coredns-8645f4b4c6-5frkg
    ....
    2019/01/08 05:06:47 [INFO] Reloading
    2019/01/08 05:06:47 [INFO] plugin/reload: Running configuration MD5 = 05514b3e44bcf4ea805c87cc6cd56c07
    2019/01/08 05:06:47 [INFO] Reloading complete
    
    # kubectl logs -f -n kube-system coredns-8645f4b4c6-lj59t
    ....
    2019/01/08 05:06:31 [INFO] Reloading
    2019/01/08 05:06:32 [INFO] plugin/reload: Running configuration MD5 = 05514b3e44bcf4ea805c87cc6cd56c07
    2019/01/08 05:06:32 [INFO] Reloading complete
    ```


## 创建 ServiceEntry 验证 DNS 解析 {#section_nwa_zyx_lpj .section}

ServiceEntry 能够在 Istio 内部的服务注册表中加入额外的条目，从而让网格中自动发现的服务能够访问和路由到这些手工加入的服务。ServiceEntry 描述了服务的属性，即包括 DNS 名称、虚拟 IP、端口、协议以及端点等。这类服务可能是网格外的 API，或者是处于网格内部但却不存在于平台的服务注册表中的条目。例如，需要和 Kubernetes 服务沟通的一组基于虚拟机VM提供的服务。

本例中，使用了通配符定义 hosts，并指定了address，如下所示。

1.  [通过 kubectl 连接 Kubernetes 集群](../../../../intl.zh-CN/用户指南/Serverless Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)，创建并拷贝以下内容到 external-svc-serviceentry.yaml 文件中，并执行`kubectl apply -f external-svc-serviceentry.yaml`命令，创建 ServiceEntry。

    ``` {#codeblock_x0a_jic_kq5}
    apiVersion: networking.istio.io/v1alpha3
    kind: ServiceEntry
    metadata:
      name: external-svc-serviceentry
    spec:
      hosts:
      - '*.test.global'
      addresses:
      - 127.255.0.2
      ports:
      - number: 8080
        name: http1
        protocol: HTTP
      location: MESH_INTERNAL
      resolution: DNS
      endpoints:
      - address: 47.111.38.80
        ports:
          http1: 15443
    ```

2.  执行如下命令查看容器istiocoredns ，可以看到上述 ServiceEntry 的域名映射关系已经被加载。

    ``` {#codeblock_kw2_3qx_led}
    # kubectl get po -n istio-system | grep istiocoredns
    istiocoredns-cdc56b67-ngtkr                 2/2     Running   0          1h
    
    # kubectl logs --tail 2 -n istio-system istiocoredns-cdc56b67-ngtkr -c istio-coredns-plugin
    2019-01-08T05:27:22.897845Z    info    Have 1 service entries
    2019-01-08T05:27:22.897888Z    info    adding DNS mapping: .test.global.->[127.255.XX.XX]
    ```

3.  执行如下命令，使用镜像 tutum/dnsutils 创建测试容器。

    ``` {#codeblock_ewx_fab_myp}
    # kubectl run dnsutils -it --image=tutum/dnsutils bash
    ```

4.  进入容器命令行后，执行 dig 查看相应的域名解析。

    ``` {#codeblock_fht_1va_en8}
    # dig +short 172.19.XX.XX A service1.test.global
    127.255.XX.XX
    # dig +short 172.19.XX.XX A service2.test.global
    127.255.XX.XX
    ```


