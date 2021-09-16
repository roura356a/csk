---
keyword: [域名解析, dnspolicy, CoreDNS, 服务发现]
---

# ACK集群内DNS原理和配置说明

在容器服务ACK集群中，推荐使用Service名作为服务的访问地址，因此需要一个ACK集群范围的DNS服务实现从Service名到Cluster IP（Service的IP地址）的解析。阿里云容器服务ACK部署了CoreDNS作为集群内的DNS服务器，为集群内的工作负载提供域名解析服务。本文介绍ACK集群DNS服务原理，并针对不同场景介绍如何进行DNS策略配置及CoreDNS配置。

在进行内置DNS配置前，请确保您已完成以下操作：

-   [创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)
-   [通过kubectl工具连接集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)

ACK集群默认部署了一套DNS服务，通过kube-dns的服务名暴露DNS服务。您可执行以下命令查看kube-dns的服务详情。

```
kubectl get svc kube-dns -n kube-system
```

预期输出：

```
NAME       TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)                  AGE
kube-dns   ClusterIP   172.24.0.10   <none>        53/UDP,53/TCP,9153/TCP   27d
```

ACK部署的DNS服务后端是两个名为CoreDNS的Pod。您可执行以下命令查看CoreDNS的Pod详情。

```
kubectl get deployment coredns -n kube-system
```

预期输出：

```
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
coredns   2/2         2                      2                   27d
```

## 集群DNS域名解析原理

ACK集群中kubelet的启动参数有`--cluster-dns=<dns-service-ip>`、`--cluster-domain=<default-local-domain>`，这两个参数分别被用来设置集群DNS服务器的IP地址和主域名后缀。

Pod内的DNS域名解析配置文件为/etc/resolv.conf，文件内容如下。

```
nameserver xx.xx.0.10
search kube-system.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```

|参数|描述|
|--|--|
|nameserver|定义DNS服务器的IP地址。|
|search|设置域名的查找后缀规则，查找配置越多，说明域名解析查找匹配次数越多。ACK集群匹配有`kube-system.svc.cluster.local`、`svc.cluster.local`、`cluster.local`3个后缀，最多进行8次查询才能得到正确解析结果，因为集群里面进行IPV4和IPV6查询各四次。|
|options|定义域名解析配置文件选项，支持多个KV值。例如该参数设置成`ndots:5`，说明如果访问的域名字符串内的点字符数量超过`ndots`值，则认为是完整域名，并被直接解析；如果不足`ndots`值，则追加search段后缀再进行查询。|

根据上述Pod内的配置，集群会将域名请求（集群内部定义的服务或是集群外部域名）查询发往集群DNS服务器获取结果。

## 集群dnsPolicy配置和场景说明

ACK支持通过dnsPolicy字段为每个Pod配置不同的DNS策略。目前ACK集群支持四种策略：

-   ClusterFirst：通过CoreDNS来做域名解析，Pod内/etc/resolv.conf配置的DNS服务地址是集群DNS服务的kube-dns地址。该策略是集群工作负载的默认策略。
-   None：忽略集群DNS策略，需要您提供dnsConfig字段来指定DNS配置信息。
-   Default：Pod直接继承集群节点的域名解析配置。即在ACK集群直接使用ECS的/etc/resolv.conf文件（文件内配置的是阿里云DNS服务）。
-   ClusterFirstWithHostNet：强制在hostNetWork网络模式下使用ClusterFirst策略（默认使用Default策略）。

针对上述四种策略，本文列举四种场景分别介绍如何配置dnsPolicy。

-   **场景一：使用ACK集群提供的CoreDNS来做域名解析**

    针对这种场景，可使用`dnsPolicy: ClusterFirst`策略。示例配置如下：

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: alpine
      namespace: default
    spec:
      containers:
      - image: alpine
        command:
          - sleep
          - "10000"
        imagePullPolicy: Always
        name: alpine
      dnsPolicy: ClusterFirst
    ```

-   **场景二：Pod层面自定义DNS配置**

    当您需要给Deployment类型的工作负载指定DNS配置时，可使用`dnsPolicy: None`策略。示例配置如下：

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: alpine
      namespace: default
    spec:
      containers:
      - image: alpine
        command:
          - sleep
          - "10000"
        imagePullPolicy: Always
        name: alpine
      dnsPolicy: None
      dnsConfig:
        nameservers: ["169.254.xx.xx"]
        searches:
        - default.svc.cluster.local
        - svc.cluster.local
        - cluster.local
        options:
        - name: ndots
          value: "2"
    ```

    其中，dnsConfig中的参数说明如下：

    |参数|描述|
    |--|--|
    |nameservers|将用作Pod的DNS服务器的IP地址列表。最多可以指定3个IP地址。当Pod的dnsPolicy设置为`None`时，列表必须至少包含一个IP地址，否则此属性是可选的。列出的DNS的IP列表将合并到基于dnsPolicy生成的域名解析文件的nameserver字段中，并删除重复的地址。|
    |searches|Pod中主机名查找的DNS搜索域列表。此属性是可选的。指定后，提供的列表将合并到从所选DNS策略生成的基本搜索域名中，并删除重复的域名。Kubernetes最多允许6个搜索域。|
    |options|可选的对象列表，其中每个对象可以具有name属性（必需）和value属性（可选）。此属性中的内容将合并到从指定的DNS策略生成的选项中，并删除重复的条目。|

    更多信息，请参见[Kubernetes官网的dns配置说明](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)。

-   **场景三：采用阿里云ECS的DNS配置**

    当您的应用Pod不需要访问集群内的其它服务，只需要通过阿里云DNS来做解析，也不希望DNS解析经过CoreDNS，可以采用`dnsPolicy: Default`策略。示例配置如下：

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: alpine
      namespace: default
    spec:
      containers:
      - image: alpine
        command:
          - sleep
          - "10000"
        imagePullPolicy: Always
        name: alpine
      dnsPolicy: Default
    ```

-   **场景四：在HostNetwork网络模式下访问集群服务**

    如果您的应用Pod使用hostNetwork:true来配置网络，Pod中运行的应用程序可以直接看到宿主机的网络接口，其DNS策略默认为Default，不能访问集群内的服务。如果您希望在此网络模式下访问集群内服务，可使用`dnsPolicy: ClusterFirstWithHostNet`策略。示例配置如下：

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: alpine
      namespace: default
    spec:
      hostNetwork: true
      dnsPolicy: ClusterFirstWithHostNet
      containers:
      - image: alpine
        command:
          - sleep
          - "10000"
        imagePullPolicy: Always
        name: alpine
    ```


## 集群CoreDNS配置

针对[场景一：使用ACK集群提供的CoreDNS来做域名解析](#li_wa7_oax_ncy)，本文将介绍ACK集群CoreDNS的默认配置及针对常见功能的扩展配置。

**说明：** 如果您安装了local-dns，则命名空间kube-system下有一个node-local-dns配置项文件，其配置原理与CoreDNS的配置原理一致。

**CoreDNS的默认配置**

在命名空间kube-system下，ACK集群有一个CoreDNS配置项（有关如何查看配置项的具体步骤，请参见[查看配置项](/cn.zh-CN/Kubernetes集群用户指南/应用/配置项及保密字典/管理配置项.md)）。其Corefile字段的文件配置内容如下（CoreDNS功能都是通过Corefile内的插件提供）。

```
  Corefile: |
    .:53 {
        errors
        health {
           lameduck 5s
        }
        ready
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          upstream
          fallthrough in-addr.arpa ip6.arpa
          ttl 30
        }
        prometheus :9153
        forward . /etc/resolv.conf
        cache 30
        loop
        reload
        loadbalance
    }
```

|参数|描述|
|--|--|
|errors|错误信息到标准输出。|
|health|CoreDNS自身健康状态报告，默认监听端口8080，一般用来做健康检查。您可以通过`http://localhost:8080/health`获取健康状态。|
|ready|CoreDNS插件状态报告，默认监听端口8181，一般用来做可读性检查。可以通过`http://localhost:8181/ready`获取可读状态。当所有插件都运行后，ready状态为200。|
|kubernetes|CoreDNS kubernetes插件，提供集群内服务解析能力。|
|prometheus|CoreDNS自身metrics数据接口。可以通过`http://localhost:9153/metrics`获取prometheus格式的监控数据。|
|forward（或proxy）|将域名查询请求转到预定义的DNS服务器。默认配置中，当域名不在kubernetes域时，将请求转发到预定义的解析器（/etc/resolv.conf）中。默认使用宿主机的/etc/resolv.conf配置。|
|cache|DNS缓存。|
|loop|环路检测，如果检测到环路，则停止CoreDNS。|
|reload|允许自动重新加载已更改的Corefile。编辑ConfigMap配置后，请等待两分钟以使更改生效。|
|loadbalance|循环DNS负载均衡器，可以在答案中随机A、AAAA、MX记录的顺序。|

**CoreDNS的扩展配置**

针对以下不同场景，您可以扩展CoreDNS的配置：

-   **场景一：开启日志服务**

    如果需将CoreDNS每次域名解析的日志打印出来，您可以开启log插件，在Corefile里加上log。示例配置如下：

    ```
      Corefile: |
        .:53 {
            errors
            log
            health {
               lameduck 5s
            }
            ready
            kubernetes cluster.local in-addr.arpa ip6.arpa {
              pods insecure
              upstream
              fallthrough in-addr.arpa ip6.arpa
              ttl 30
            }
            prometheus :9153
            forward . /etc/resolv.conf
            cache 30
            loop
            reload
            loadbalance
        }
    ```

-   **场景二：特定域名使用自定义DNS服务器**

    如果example.com类型后缀的域名需要经过自建DNS服务器（IP为10.10.0.10）进行解析的话，您可为域名配置一个单独的服务块。示例配置如下：

    ```
    example.com:53 {
      errors
      cache 30
      forward . 10.10.0.10
    }
    ```

    完整配置如下：

    ```
      Corefile: |
        .:53 {
            errors
            health {
               lameduck 5s
            }
            ready
            kubernetes cluster.local in-addr.arpa ip6.arpa {
              pods insecure
              upstream
              fallthrough in-addr.arpa ip6.arpa
              ttl 30
            }
            prometheus :9153
            forward . /etc/resolv.conf
            cache 30
            loop
            reload
            loadbalance
        }
        example.com:53 {
            errors
            cache 30
            forward . 10.10.0.10
        }
    ```

-   **场景三：外部域名完全使用自建DNS服务器**

    如果您需要使用的自建DNS服务的域名没有统一的域名后缀，您可以选择所有集群外部域名走自建DNS服务器（此时需要您将自建的DNS服务不能解析的域名转发到阿里云DNS，禁止直接更改集群ECS上的/etc/resolv.conf文件）。例如，您自建的DNS服务器IP为10.10.0.10和10.10.0.20，可以更改forward参数进行配置。示例配置如下：

    ```
      Corefile: |
        .:53 {
            errors
            health {
               lameduck 5s
            }
            ready
            kubernetes cluster.local in-addr.arpa ip6.arpa {
              pods insecure
              upstream
              fallthrough in-addr.arpa ip6.arpa
              ttl 30
            }
            prometheus :9153
            forward . 10.10.0.10 10.10.0.20
            cache 30
            loop
            reload
            loadbalance
        }
    ```

-   **场景四：自定义hosts**

    如果您需要为特定域名指定hosts，如为www.example.com指定IP为127.0.0.1，可以使用hosts插件来配置。示例配置如下：

    ```
      Corefile: |
        .:53 {
            errors
            health {
               lameduck 5s
            }
            ready
            
            hosts {
              127.0.0.1 www.example.com
              fallthrough
            }
          
            kubernetes cluster.local in-addr.arpa ip6.arpa {
              pods insecure
              upstream
              fallthrough in-addr.arpa ip6.arpa
              ttl 30
            }
            prometheus :9153
            forward . /etc/resolv.conf
            cache 30
            loop
            reload
            loadbalance
        }
    ```

    **说明：** 请配置fallthrough，否则会造成非定制hosts域名解析失败。

-   **场景五：集群外部访问集群内服务**

    如果您希望运行在集群ECS上的进程能够访问到集群内的服务，虽然可以通过将ECS的/etc/resolv.conf文件内nameserver配置为集群kube-dns的ClusterIP地址来达到目的，但不推荐您直接更改ECS的/etc/resolv.conf文件的方式来达到任何目的。

    内网场景下，您可以将集群内的服务通过内网SLB进行暴露，然后在[云解析PrivateZone控制台](https://dns.console.aliyun.com/#/dns/domainList)通过添加A记录到该SLB的内网IP进行解析。具体操作，请参见[添加解析记录](https://help.aliyun.com/document_detail/64635.html)。

-   **场景六：统一域名访问服务或是在集群内对域名的做CNAME解析**

    您可以实现在公网、内网和集群内部通过统一域名foo.example.com访问您的服务，原理如下：

    -   集群内的服务`foo.default.svc.cluster.local`通过公网SLB进行了暴露，且有域名`foo.example.com`解析到该公网SLB的IP。
    -   集群内服务`foo.default.svc.cluster.local`通过内网SLB进行了暴露，且通过[云解析PrivateZone](https://dns.console.aliyun.com/#/dns/domainList)在VPC内网中将`foo.example.com`解析到该内网SLB的IP。具体步骤，请参见上述[为特定域名指定hosts](#li_ejo_1bo_ag5)。
    -   在集群内部，您可以通过Rewrite插件将`foo.example.com` CNAME到`foo.default.svc.cluster.local`。示例配置如下：

        ```
          Corefile: |
            .:53 {
                errors
                health {
                   lameduck 5s
                }
                ready
                
                rewrite stop {
                  name regex foo.example.com foo.default.svc.cluster.local
                  answer name foo.default.svc.cluster.local foo.example.com 
                }
        
                kubernetes cluster.local in-addr.arpa ip6.arpa {
                  pods insecure
                  upstream
                  fallthrough in-addr.arpa ip6.arpa
                  ttl 30
                }
                prometheus :9153
                forward . /etc/resolv.conf
                cache 30
                loop
                reload
                loadbalance
            }
        ```


