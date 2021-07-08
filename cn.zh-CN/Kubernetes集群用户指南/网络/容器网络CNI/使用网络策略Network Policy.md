---
keyword: [网络策略, Network Policy, k8s, terway]
---

# 使用网络策略Network Policy

Kubernetes网络策略（Network Policy）提供基于策略的网络控制。当您使用Terway容器网络时，如果您希望在IP地址或者端口层面控制网络流量，您可以为集群中特定应用使用网络策略。本文介绍如何使用ACK集群的网络策略及常用的使用场景。

-   [创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

    **说明：** 您在创建集群时，需设置**网络插件**为**Terway**。

-   [通过kubectl管理Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)。

## 开启网络策略

**说明：**

-   如果您想通过控制台的方式使用网络策略功能，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)申请成为白名单用户。
-   如果通过命令行方式使用网络策略功能，无需申请成为白名单用户。

您可以在创建Terway集群时开启网络策略功能，也可以在创建Terway集群后再开启网络策略功能：

-   如果您在创建Terway集群时，选中了**NetworkPolicy支持**配置，可自动开启网络策略功能。具体操作，请参见[创建ACK Pro版集群](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。

-   如果您在创建Terway集群时，未开启网络策略功能，可以通过**配置管理**入口，在kube-system命名空间下的**eni-config**配置项中，将disable\_network\_policy设置成false，以开启网络策略功能。关于配置项的设置，请参见[修改配置项](/cn.zh-CN/Kubernetes集群用户指南/应用/配置项及保密字典/管理配置项.md)。

    ![配置项](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9910454261/p287848.png)


## 使用场景

本文介绍在以下场景中如何设置网络策略：

-   [通过网络策略限制服务只能被带有特定标签的应用访问](#section_yys_08i_fxv)
-   [通过网络策略限制可访问公网服务的来源IP网段](#section_887_r9y_k1b)
-   [通过网络策略限制一个Pod只能访问指定地址](#section_i3o_oay_a0s)
-   [通过网络策略控制命名空间下Pod公网访问权限](#section_zrr_z09_kq5)

以上场景均使用在[创建可以被其他Pod正常访问的nginx测试应用](#section_lso_lzv_tq4)中创建的应用。

## 创建可以被其他Pod正常访问的nginx测试应用

**通过控制台操作**

1.  创建名称为nginx的应用，并通过Service将其公开。具体步骤，请参见[使用镜像创建无状态Deployment应用](/cn.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建无状态工作负载Deployment.md)。

    部分示例配置如下。

    |配置项|参数|示例值|
    |---|--|---|
    |应用基本信息|应用名称|nginx|
    |副本数量|1|
    |容器配置|镜像名称|nginx|
    |服务创建|名称|nginx|
    |类型|    -   负载均衡
    -   公网访问
    -   新建SLB |
    |端口映射|    -   名称：nginx
    -   服务端口：80
    -   容器端口：80
    -   协议：TCP |

2.  创建用于测试访问名称为busybox的客户端应用，用于测试访问上一步中创建的名为nginx的Service。具体步骤，请参见[使用镜像创建无状态Deployment应用](/cn.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建无状态工作负载Deployment.md)。

    部分示例配置如下。

    |配置项|参数|示例值|
    |---|--|---|
    |应用基本信息|应用名称|busybox|
    |副本数量|1|
    |容器启动项|镜像名称|busybox|
    |容器启动项|选中**stdin**和**tty**|

3.  验证busybox客户端应用是否可以访问nginx Service。

    1.  在无状态页面，找到并单击busybox名称。

    2.  在**容器组**页签下的busybox-\{hash值\}容器组右侧，单击**终端**。

        ![busybox](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7408634261/p286133.png)

    3.  在busybox的命令行终端中执行`wget nginx`测试访问nginx。

        ![connection](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0800444261/p286241.png)

        当出现以上信息时，说明您可以在busybox中访问nginx Service。


**通过命令行操作**

1.  执行以下命令，创建一个nginx的应用，并通过名称为nginx的Service与其通信。

    创建nginx应用：

    ```
    kubectl run nginx --image=nginx
    ```

    预期输出：

    ```
    pod/nginx created
    ```

    查看Pod是否启动：

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx                    1/1     Running   0          45s
    ```

    创建名为nginx的Service：

    ```
    kubectl expose pod nginx --port=80
    ```

    预期输出：

    ```
    service/nginx exposed
    ```

    ```
    kubectl get service
    ```

    预期输出：

    ```
    NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
    kubernetes   ClusterIP   172.XX.XX.1     <none>        443/TCP   30m
    nginx        ClusterIP   172.XX.XX.48    <none>        80/TCP    12s
    ```

2.  执行以下命令，创建名称为busybox的Pod，访问名为nginx的Service。

    ```
    kubectl run busybox --rm -ti --image=busybox /bin/sh
    ```

    预期输出：

    ```
    If you don't see a command prompt, try pressing enter.
    / #
    ```

    ```
    wget nginx
    ```

    预期输出：

    ```
    Connecting to nginx (172.XX.XX.48:80)
    saving to 'index.html'
    index.html           100% |****************************************************************************************************************************************************|   612  0:00:00 ETA
    'index.html' saved
    ```


## 通过网络策略限制服务只能被带有特定标签的应用访问

**通过控制台操作**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**网络** \> **网络策略**。

5.  在**网络策略**页面右上方，单击**创建**，然后在**创建面板**中配置网络策略。

    |参数|描述|
    |--|--|
    |名称|设置网络策略的名称，本文示例填写：access-nginx。|
    |命名空间|设置放置网络策略的命名空间。|
    |Pod选择器|设置使用网络策略的Pod。本文示例设置如下：

    -   设置类型为：**无状态**
    -   设置工作负载为：**nginx**
    -   设置标签为：**app=nginx**
**说明：** 如果Pod选择器设置为空，则对命名空间下所有Pod生效。 |
    |来源|每个网络策略可包含一个来源（ingress规则）的白名单列表。 每个规则都允许同时匹配来源规则和特定端口部分的流量。    -   **规则**：
        -   podSelector：此选择器将在与网络策略同命名空间中选择特定的 Pod，并将其允许作为入站流量来源。
        -   namespaceSelector：此选择器将选择特定的命名空间，并将所有Pod 用作其入站流量来源。
        -   ipBlock：此选择器将选择特定的IP CIDR范围以用作入站流量来源。
    -   **端口**：支持TCP和UDP协议。
**说明：** 如果不添加任何规则，表示不允许访问任何Pod与选择的Pod进行访问。

本示例不添加任何来源规则。 |
    |去向|每个网络策略可包含一个egress规则的白名单列表。每个规则都允许匹配去向规则和特定端口部分的流量。    -   **规则**：
        -   podSelector：此选择器将在与网络策略同命名空间中选择特定的 Pod，并将其允许作为出站流量目的地。
        -   namespaceSelector：此选择器将选择特定的命名空间，并将所有Pod用作其出站流量目的地。
        -   ipBlock：此选择器将选择特定的IP CIDR范围以出站流量目的地。
    -   **端口**：支持TCP和UDP协议。
本示例不添加任何去向规则。 |

6.  在busybox命令行终端执行`wget nginx`命令，测试与nginx Service的通信。具体操作，请参见[步骤3](#step_a7x_mb4_jf4)。

    因在网络策略没有允许busybox访问，所以访问会超时。

    ![timeout](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0800444261/p286629.png)

7.  修改网络策略为允许busybox的应用访问。

    1.  在网络策略列表中，找到access-nginx网络策略并在其右侧，单击**编辑**。

    2.  添加来源规则。

        在**来源**右侧，单击**+添加**，并继续以下操作：

        -   在**规则**右侧，单击**+添加**。添加podSelector的访问，规则设置如下：

            |参数|示例值|
            |--|---|
            |选择器|podSelector|
            |类型|无状态|
            |工作负载|busybox|

        -   在**端口**右侧，单击**+添加**。端口设置如下：

            |参数|示例值|
            |--|---|
            |协议|TCP|
            |端口|80|

    3.  单击**下一步**，然后单击**确定**。

    4.  执行`wget -O /dev/null nginx`命令，测试修改网络策略后busybox与nginx的通信。

        网络策略增加busybox应用的规则后，busybox可以正常访问nginx。

        ![正常访问](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0800444261/p286712.png)


**通过命令行操作**

1.  使用以下YAML模板并执行命令`vim policy.yaml`，创建名为policy.yaml文件。

    ```
    vim policy.yaml
    ```

    以下为YAML文件内容。

    ```
    kind: NetworkPolicy
    apiVersion: networking.k8s.io/v1
    metadata:
      name: access-nginx
    spec:
      podSelector:
        matchLabels:
          run: nginx
      ingress:
      - from:
        - podSelector:
            matchLabels:
              access: "true"
    ```

2.  执行以下命令，根据上述的policy.yaml文件创建网络策略。

    ```
    kubectl apply -f policy.yaml 
    ```

    预期输出：

    ```
    networkpolicy.networking.k8s.io/access-nginx created
    ```

3.  执行以下命令，当没有定义访问标签时，测试访问名为nginx的Service，请求会超时，无法访问。

    ```
    kubectl run busybox --rm -ti --image=busybox /bin/sh
    ```

    ```
    wget nginx
    ```

    预期输出：

    ```
    Connecting to nginx (172.19.8.48:80)
    wget: can't connect to remote host (172.19.8.48): Connection timed out
    ```

4.  执行以下命令，定义访问标签，测试访问nginx Service。

    ```
    kubectl run busybox --rm -ti --labels="access=true" --image=busybox /bin/sh
    ```

    ```
    wget nginx
    ```

    预期输出：

    ```
    Connecting to nginx (172.21.x.114:80)
    saving to 'index.html'
    index.html           100% |****************************************************************************************************************************************************|   612  0:00:00 ETA
    'index.html' saved
    ```

    连接nginx的进度为100%时，说明请求成功，可以正常访问nginx服务。


## 通过网络策略限制可访问公网服务的来源IP网段

**通过控制台操作**

1.  为nginx Service设置网络策略，相关配置，请参见[通过网络策略限制服务只能被带有特定标签的应用访问](#section_yys_08i_fxv)。

2.  在服务列表中的**外部端点**列下，查看[创建可以被其他Pod正常访问的nginx测试应用](#section_lso_lzv_tq4)中nginx Service的公网访问地址（47.xxx.xx.x），并在浏览器中访问该地址。

    ![en](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5511444261/p286719.png)

    由于已为nginx添加网络策略，默认访问nginx失败。

3.  通过网络策略增加允许访问的网段来允许客户端访问。

    1.  通过浏览器访问[myip.ipip.net](https://myip.ipip.net/)获取本机的IP地址。

    2.  在网络策略列表中，找到access-nginx网络策略并在其右侧，单击**编辑**，然后在**编辑**面板中修改规则。

        在**来源**右侧，单击**+添加**，并继续以下操作：

        -   在**规则**右侧，单击**+添加**。规则中增加本机的IP地址的访问：

            |参数|示例值|
            |--|---|
            |选择器|ipBlock|
            |cidr|<本机的IP地址\>/32例如：42.xx.xx.xx/32 |

        -   在**规则**右侧，单击**+添加**。规则中增加阿里云SLB的健康检查网段的访问：

            |参数|示例值|
            |--|---|
            |选择器|ipBlock|
            |cidr|100.0.0.0/8|

        -   在**端口**右侧，单击**+添加**。端口设置如下：

            |参数|示例值|
            |--|---|
            |协议|TCP|
            |端口|80|

        ![ipblock](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4793444261/p286736.png)

    3.  单击**下一步**，然后单击**确定**。

    4.  访问nginx外部端点（47.xxx.xx.x）。

        增加网络策略后客户端可以通过公网SLB访问nginx。


**通过命令行操作**

1.  执行以下命令，为上述名为nginx的应用创建阿里云SLB服务，指定`type=LoadBalancer`来向外网用户暴露nginx服务。

    nginx-service.yaml文件模板如下。

    ```
    vim nginx-service.yaml
    # 以下为YAML文件内容。
    
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        run: nginx
      name: nginx-slb
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

    ```
    kubectl apply -f nginx-service.yaml 
    ```

    预期输出：

    ```
    service/nginx-slb created
    ```

    ```
    kubectl get service nginx-slb
    ```

    预期输出：

    ```
    NAME        TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE
    nginx-slb   LoadBalancer   172.19.xx.xxx   47.110.xxx.xxx   80:32240/TCP   8m
    ```

2.  执行以下命令，访问刚刚创建的SLB的IP地址47.110.xxx.xxx，访问失败。

    ```
    wget 47.110.xxx.xxx
    ```

    预期输出：

    ```
    --2018-11-21 11:46:05--  http://47.110.xx.xxx/
    Connecting to 47.110.XX.XX:80... failed: Connection refused.
    ```

    **说明：**

    访问失败的原因是：

    -   配置的nginx Service只能被带有特定标签即`access=true`的应用访问。
    -   访问SLB的IP地址，是从外部访问Kubernetes，与通过网络策略限制服务只能被带有特定标签的应用访问不同。
    解决方法：修改网络策略，增加允许访问的来源IP地址段。

3.  执行以下命令，查看本地的IP地址。

    ```
    curl myip.ipip.net
    ```

    预期输出：

    ```
    当前IP：10.0.x.x来自于：中国 北京 北京        #此处仅为示例，具体请以实际设备为准。
    ```

4.  执行以下命令，修改已经创建的policy.yaml文件。

    ```
    vim policy.yaml
    # 以下为YAML文件内容。
    kind: NetworkPolicy
    apiVersion: networking.k8s.io/v1
    metadata:
      name: access-nginx
    spec:
      podSelector:
        matchLabels:
          run: nginx
      ingress:
      - from:
        - podSelector:
            matchLabels:
              access: "true"
        - ipBlock:
            cidr: 100.64.0.0/10
        - ipBlock:
            cidr: 10.0.0.1/24      #本地IP地址，此处仅为示例，具体请以实际设备为准。
    ```

    ```
    kubectl apply -f policy.yaml 
    ```

    预期输出：

    ```
    networkpolicy.networking.k8s.io/access-nginx unchanged
    ```

    **说明：**

    -   有些网络的出口有多个IP地址，这里请使用/24的地址范围。
    -   SLB健康检查地址在`100.64.0.0/10`地址段内，因此请务必配置`100.64.0.0/10`。
5.  执行以下命令，测试访问Nginx服务。

    ```
    kubectl run busybox --rm -ti --labels="access=true" --image=busybox /bin/sh
    ```

    ```
    wget 47.110.XX.XX
    ```

    预期输出：

    ```
    Connecting to 47.110.XX.XX (47.110.XX.XX:80)
    index.html           100% |***********************************************************|   612  0:00:00 ETA
    ```

    连接进度显示100%时，说明成功访问Nginx服务。


## 通过网络策略限制一个Pod只能访问指定地址

**通过控制台操作**

本文以www.aliyun.com和registry.aliyuncs.com为例，说明通过设置网络策略一个Pod只能访问registry.aliyuncs.com。

1.  通过[Public DNS](https://dns.google.com/)网站查询registry.aliyuncs.com地址绑定的IP地址（120.55.105.209）。

2.  在网络策略中创建规则限制busybox应用只能访问registry.aliyuncs.com。

    1.  在**网络策略**页面右上角，单击**创建**，然后在**创建**面板中配置网络策略规则。

        关于具体的参数描述，请参见[通过网络策略限制服务只能被带有特定标签的应用访问](#section_yys_08i_fxv)。示例配置如下。

        -   设置**名称**为busybox-policy。
        -   设置Pod选择器。

            |参数|示例值|
            |--|---|
            |类型|无状态|
            |工作负载|busybox|
            |标签|app=busybox|

        -   在**去向**右侧，单击**+添加**，并继续以下操作。
            -   在**规则**右侧，单击**+添加**。添加ipBlock为前面获取的registry.aliyuncs.com的解析的IP地址（120.55.105.209）/32的规则。

                |参数|示例值|
                |--|---|
                |选择器|ipBlock|
                |cidr|120.55.105.209/32|

        -   在**去向**右侧，单击**+添加**。添加命名空间选择器为全部命名空间的UDP 53端口的访问的规则，用来保障应用可以解析DNS。

            -   在**规则**右侧，单击**+添加**。添加命名空间选择器为全部的规则。

                |参数|示例值|
                |--|---|
                |选择器|namespace|
                |命名空间|全部|

            -   在**端口**右侧，单击**+添加**。添加UDP 53的规则用来保障应用可以解析DNS。端口设置如下：

                |参数|示例值|
                |--|---|
                |协议|UDP|
                |端口|53|

            去向规则配置如下图。

            ![dns](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596054261/p286769.png)

    2.  单击**下一步**，然后单击**确定**。

    3.  在busybox终端执行以下命令分别访问www.aliyun.com和registry.aliyuncs.com。

        ```
        nc -vz -w 1 www.aliyunc.com 443
        ```

        ```
        nc -vz -w 1 registry.aliyuncs.com 443
        ```

        增加网络策略后，busybox应用只能访问registry.aliyuncs.com而无法访问其他地址。

        ![dns](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5852054261/p286772.png)


**通过命令行操作**

1.  执行以下命令，获取www.aliyun.com域名解析到的IP地址列表。

    ```
    dig +short www.aliyun.com
    ```

    预期输出：

    ```
    www-jp-de-intl-adns.aliyun.com.
    www-jp-de-intl-adns.aliyun.com.gds.alibabadns.com.
    v6wagbridge.aliyun.com.
    v6wagbridge.aliyun.com.gds.alibabadns.com.
    106.XX.XX.21
    140.XX.XX.4
    140.XX.XX.13
    140.XX.XX.3
    ```

2.  执行以下命令，创建busybox-policy文件。

    ```
    vim busybox-policy.yaml
    # 以下为YAML文件内容。
    kind: NetworkPolicy
    apiVersion: networking.k8s.io/v1
    metadata:
      name: busybox-policy
    spec:
      podSelector:
        matchLabels:
          run: busybox
      egress:
      - to:
        - ipBlock:
            cidr: 106.XX.XX.21/32
        - ipBlock:
            cidr: 140.XX.XX.4/32
        - ipBlock:
            cidr: 140.XX.XX.13/32
        - ipBlock:
            cidr: 140.XX.XX.3/32
      - to:
        - ipBlock:
            cidr: 0.0.0.0/0
        - namespaceSelector: {}
        ports:
        - protocol: UDP
          port: 53
    ```

    **说明：** 在busybox-policy文件中，配置了去向规则（Egress），限制应用的对外访问。在这里需配置允许UDP请求，否则无法做DNS解析。

3.  执行以下命令，根据busybox-policy文件创建网络策略。

    ```
    kubectl apply -f busybox-policy.yaml 
    ```

    预期输出：

    ```
    networkpolicy.networking.k8s.io/busybox-policy created
    ```

4.  执行以下命令，访问www.aliyun.com之外的网站，例如，www.taobao.com。

    ```
    kubectl run busybox --rm -ti --image=busybox /bin/sh
    ```

    ```
    wget www.taobao.com
    ```

    预期输出：

    ```
    Connecting to www.taobao.com (64.13.XX.XX:80)
    wget: can't connect to remote host (64.13.XX.XX): Connection timed out
    ```

    出现can't connect to remote host时，说明失败访问服务。

5.  执行以下命令，访问www.aliyun.com。

    ```
    wget www.aliyun.com
    ```

    预期输出：

    ```
    Connecting to www.aliyun.com (140.205.XX.XX:80)
    Connecting to www.aliyun.com (140.205.XX.XX:443)
    wget: note: TLS certificate validation not implemented
    index.html           100% |***********************************************************|  462k  0:00:00 ETA
    ```

    进行显示100%，说明成功访问服务。


## 通过网络策略控制命名空间下Pod公网访问权限

**说明：** 此操作有可能影响线上正在访问公网的服务，建议在一个空的命名空间中进行以下操作。

**通过控制台操作**

1.  在**网络策略**页面右上角，单击**创建**，然后在**创建**面板中配置网络策略规则。

    关于具体的参数描述及操作，请参见[通过网络策略限制服务只能被带有特定标签的应用访问](#section_yys_08i_fxv)。示例配置如下。

    |参数|示例值|
    |--|---|
    |名称|deny-public-net|
    |Pod选择器|设置类型为**全部**。|
    |来源|添加以下两条规则：    -   设置**namespaceSelector**的**全部**允许的规则。
    -   设置**ipBlock**的**全部**允许的规则。
![来源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5852054261/p286827.png) |
    |去向|添加只允许访问内网的规则：    -   设置**namespaceSelector**的**全部**允许的规则，允许Pod访问内网的所有Pod。
    -   设置**ipBlock**的三条内网网段的规则，包括以下三个内网网段：

        -   10.0.0.0/8
        -   172.16.0.0/12
        -   192.168.0.0/16
**说明：** 不可同时在一条ipBlock规则中同时添加多个网段。

![公网 去向](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5852054261/p286832.png) |

2.  单击**下一步**，然后单击**确定**。

3.  通过上文[创建可以被其他Pod正常访问的nginx测试应用](#section_lso_lzv_tq4)中创建的busybox应用，执行以下命令测试Pod的公网访问权限。

    ```
    nc -vz -w 1 www.aliyunc.com 443
    ```

    ```
    nc -vz -w 1 192.168.0.120:10255
    ```

    Pod只能访问内网的地址而无法访问公网的地址。

    ![Pod公网访问](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5852054261/p286841.png)


**通过命令行操作**

1.  执行以下命令，创建一个测试的命名空间来验证限制的能力。

    创建一个test-np的命名空间。

    ```
    kubectl create ns test-np
    ```

    预期输出：

    ```
    namespace/test-np created
    ```

2.  执行以下命令，为这个命名空间设置默认的只允许主动访问私网的网络策略规则。

    ```
    vim default-deny.yaml
    # 以下为YAML文件内容。
    kind: NetworkPolicy
    apiVersion: networking.k8s.io/v1
    metadata:
      namespace: test-np
      name: deny-public-net
    spec:
      podSelector: {}
      ingress:
      - from:
        - ipBlock:
            cidr: 0.0.0.0/0
      egress:
      - to:
        - ipBlock:
            cidr: 192.168.0.0/16
        - ipBlock:
            cidr: 172.16.0.0/12
        - ipBlock:
            cidr: 10.0.0.0/8
    ```

    ```
    kubectl apply -f default-deny.yaml
    ```

    预期输出：

    ```
    networkpolicy.networking.k8s.io/deny-public-net created
    ```

    ```
    kubectl get networkpolicy -n test-np
    ```

    预期输出：

    ```
    NAME                              POD-SELECTOR          AGE
    deny-public-net                   <none>                1m
    ```

3.  执行以下命令，为特殊标签的Pod设置允许访问公网。

    示例标签设置为`public-network=true`。

    ```
    vim allow-specify-label.yaml
    # 以下为YAML文件内容。
    kind: NetworkPolicy
    apiVersion: networking.k8s.io/v1
    metadata:
      name: allow-public-network-for-labels
      namespace: test-np
    spec:
      podSelector:
        matchLabels:
          public-network: "true"
      ingress:
      - from:
        - ipBlock:
            cidr: 0.0.0.0/0
      egress:
      - to:
        - ipBlock:
            cidr: 0.0.0.0/0
    ```

    ```
    kubectl apply -f allow-specify-label.yaml
    ```

    预期输出：

    ```
    networkpolicy.networking.k8s.io/allow-public-network-for-labels created
    ```

    ```
    kubectl get networkpolicy -n test-np
    ```

    预期输出：

    ```
    NAME                              POD-SELECTOR          AGE
    allow-public-network-for-labels   public-network=true    1m
    deny-public-net                   <none>                 3m
    ```

4.  执行以下命令验证无特殊标签的Pod不能访问公网。

    ```
    kubectl run -it --namespace test-np --rm --image busybox  busybox-intranet
    ```

    ```
    ping aliyun.com
    ```

    预期输出：

    ```
    PING aliyun.com (106.11.2xx.xxx): 56 data bytes
    ^C
    --- aliyun.com ping statistics ---
    9 packets transmitted, 0 packets received, 100% packet loss
    ```

    显示0 packets received时，说明访问失败。

    **说明：** 访问失败的原因是：由于通过deny-public-net的网络策略规则限制了test-np的这个命名空间下的Pod的默认的公网访问，所以它下面启动的默认标签的Pod无法访问公网。

5.  执行以下命令验证带有public-network=true的Pod可以访问服务。

    ```
    kubectl run -it --namespace test-np --labels public-network=true --rm --image busybox  busybox-internet
    ```

    ```
    ping aliyun.com
    ```

    预期输出：

    ```
    PING aliyun.com (106.11.1xx.xx): 56 data bytes
    64 bytes from 106.11.1xx.xx: seq=0 ttl=47 time=4.235 ms
    64 bytes from 106.11.1xx.xx: seq=1 ttl=47 time=4.200 ms
    64 bytes from 106.11.1xx.xx: seq=2 ttl=47 time=4.182 ms
    ^C
    --- aliyun.com ping statistics ---
    3 packets transmitted, 3 packets received, 0% packet loss
    round-trip min/avg/max = 4.182/4.205/4.235 ms
    ```

    显示0% packet loss时，说明成功访问服务。

    **说明：** 可以正常访问的原因是：由于在allow-public-network-for-labels的网络策略的规则中允许了带有public-network=true的Pod的公网访问，所以在带有这个Label的busybox-internet的Pod可以访问公网。


