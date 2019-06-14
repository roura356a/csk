# 通过界面创建路由（Ingress） {#concept_qm4_kyn_vdb .concept}

阿里云容器服务 Web 界面集成了路由（Ingress）服务，您可通过 Web 界面快速创建路由服务，构建灵活可靠的流量接入层。

## 前提条件 {#section_kj5_nyn_vdb .section}

-   您已经成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)，并且集群中 Ingress controller 正常运行。
-   SSH 登录到 Master 节点，参见[SSH访问Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/SSH访问Kubernetes集群.md#)。

## 步骤1 创建 deployment 和服务 {#section_c1p_ryn_vdb .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **无状态**，进入无状态（Deployment）页面。
3.  单击页面右上角**使用模板创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156047881610771_zh-CN.png)

4.  选择所需的集群和命名空间，选择样例模板或自定义，然后单击**创建**。

    本例中，示例中创建3个nginx应用，一个代表旧的应用old-nginx，一个代表新的应用 new-nginx，此外创建一个domain-nginx应用，用于测试集群访问域名。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156047881610772_zh-CN.png)

    old-nginx的编排模板如下所示：

    ``` {#codeblock_6by_q3i_p5n}
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: old-nginx
    spec:
      replicas: 2
      selector:
        matchLabels:
          run: old-nginx
      template:
        metadata:
          labels:
            run: old-nginx
        spec:
          containers:
          - image: registry.cn-hangzhou.aliyuncs.com/xianlu/old-nginx
            imagePullPolicy: Always
            name: old-nginx
            ports:
            - containerPort: 80
              protocol: TCP
          restartPolicy: Always
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: old-nginx
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: old-nginx
      sessionAffinity: None
      type: NodePort
    ```

    new-nginx的编排模板如下所示：

    ``` {#codeblock_y9z_02x_l36}
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: new-nginx
    spec:
      replicas: 1
      selector:
        matchLabels:
          run: new-nginx
      template:
        metadata:
          labels:
            run: new-nginx
        spec:
          containers:
          - image: registry.cn-hangzhou.aliyuncs.com/xianlu/new-nginx
            imagePullPolicy: Always
            name: new-nginx
            ports:
            - containerPort: 80
              protocol: TCP
          restartPolicy: Always
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: new-nginx
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: new-nginx
      sessionAffinity: None
      type: NodePort
    ```

    domain-nginx应用的编排模板如下所示：

    ``` {#codeblock_pcs_ngt_ai3}
    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: domain-nginx
      labels:
        app: nginx
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9 # replace it with your exactly <image_name:tags>
            ports:
            - containerPort: 80
    
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: domain-nginx
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      sessionAffinity: None
      type: NodePort
    ```

5.  单击左侧导航栏中的**路由与负载均衡** \> **服务** ，进入服务列表页面。

    等待服务创建完成后，在服务列表，您可看到本示例创建的服务。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156047881710773_zh-CN.png)


## 步骤2 创建路由 {#section_gwv_tyn_vdb .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**路由与负载均衡** \> **路由**，进入路由页面。
3.  选择所需的集群和命名空间，单击页面右上角的**创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156047881710774_zh-CN.png)

4.  在弹出的路由创建对话框中，首先配置路由名称，本例为 nginx-ingress。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156047881710775_zh-CN.png)

5.  对路由规则进行配置。

    路由规则是指授权入站到达集群服务的规则，支持 http/https 规则，配置项包括域名\(虚拟主机名称\)、URL 路径、服务名称、端口配置和路由权重等。详细的信息请参见[路由配置说明](intl.zh-CN/用户指南/Kubernetes集群/网络管理/路由配置说明.md#)。

    本例中配置添加一条复杂的路由规则，配置集群默认的测试域名和虚拟主机名称，展示基于域名的路由服务。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156047881710776_zh-CN.png)

    -   基于默认域名的简单路由，即使用集群的默认域名对外提供访问服务。
        -   **域名配置**：使用集群的默认域名，本例中是 `test.[cluster-id].[region-id].alicontainer.com`。

            在创建路由对话框中，会显示该集群的默认域名，域名格式是 `*.[cluster-id].[region-id].alicontainer.com`；您也可在集群的基本信息页面中获取。

        -   **服务配置**：配置服务的访问路径、名称以及端口。
            -   访问路径配置：您可指定服务访问的 URL 路径，默认为根路径/，本例中不做配置。每个路径 （path）都关联一个 backend （服务），在阿里云 SLB 将流量转发到 backend 之前，所有的入站请求都要先匹配域名和路径。
            -   服务配置：支持服务名称、端口、服务权重等配置，即 backend 配置。同一个访问路径下，支持多个服务的配置，Ingress的流量会被切分，并被转发到它所匹配的 backend。
    -   基于域名的简单扇出路由。本例中使用一个虚拟的主机名称作为测试域名对外提供访问服务，为两个服务配置路由权重，并为其中一个服务设置灰度发布规则。若您在生产环境中，可使用成功备案的域名提供访问服务。
        -   **域名配置**：本例中使用测试域名 `foo.bar.com`。

            您需要修改 hosts 文件添加一条域名映射规则。

            ``` {#codeblock_fki_7s5_grd}
            118.178.108.143 foo.bar.com       #IP即是Ingress的Address
            ```

        -   **服务配置**：配置服务的访问路径、服务名称、服务端口和服务权重。
            -   访问路径配置：指定服务访问的 URL 路径。本例中不做配置，保留根路径/。
            -   服务名称：本例中设置新旧两个服务 nginx-new 和 nginx-old 。
            -   服务端口：暴露80端口。
            -   权重设置：设置该路径下多个服务的权重。服务权重采用相对值计算方式，默认值为100，如本例中所示，新旧两个版本的服务权重值都是50，则表示两个服务的权重比例都是50%。
6.  配置灰度发布。

    **说明：** 目前阿里云容器服务Kubernetes Ingress Controller需要0.12.0-5及其以上版本才支持流量切分特性。

    容器服务支持多种流量切分方式，适用于灰度发布以及AB测试场景。

    1.  基于Request Header的流量切分
    2.  基于Cookie的流量切分
    3.  基于Query Param的流量切分
    设置灰度规则后，请求头中满足灰度发布匹配规则的请求才能被路由到新版本服务new-nginx中。如果该服务设置了100%以下的权重比例，满足灰度规则的请求会继续依据权重比例路由到对应服务。

    在本例中，设置Header请求头带有`foo=^bar$`的灰度发布规则，仅带有该请求头的客户端请求才能访问到new-nginx 服务。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156047881710777_zh-CN.png)

    -   **服务**：路由规则配置的服务。
    -   **类型**：支持Header（请求头）、Cookie和Query（请求参数）的匹配规则。
    -   **名称和匹配值**：用户自定义的请求字段，名称和匹配值为键值对。
    -   **匹配规则**：支持正则匹配和完全匹配。
7.  配置注解。

    单击**重定向注解**，可为路由添加一条典型的重定向注解。即 `nginx.ingress.kubernetes.io/rewrite-target：/`，表示将/path路径重定向到后端服务能够识别的根路径/上面。

    **说明：** 本例中未对服务配置访问路径，因此不需要配置重定向注解。重定向注解的作用是使Ingress以根路径转发到后端，避免访问路径错误配置而导致的404错误。

    您也可单击**添加**按钮，输入注解名称和值，即Ingress的annotation键值对，Ingress的注解参见[https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156047881810778_zh-CN.png)

8.  配置 TLS。勾选**开启** TLS，配置安全的路由服务。具体可参见[配置安全的路由服务](intl.zh-CN/用户指南/Kubernetes集群/网络管理/Ingress 支持.md#section_j4d_jrs_vdb)。
    -   您可选择使用已有密钥。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156047881810779_zh-CN.png)

        1.  登录 master 节点，创建tls.key和 tls.crt。

            ``` {#codeblock_h8g_33p_uc0}
            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
            ```

        2.  创建一个 secret。

            ``` {#codeblock_fja_6l2_0yk}
            kubectl create secret tls foo.bar --key tls.key --cert tls.crt
            ```

        3.  执行命令 `kubectl get secret`，您可看到该 secret 已经成功创建。在 Web 界面可选择创建的foo.bar这个 secret。
    -   您可选择在 TLS 界面上利用已创建的 TLS 私钥和证书，一键创建 secret。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156047881810781_zh-CN.png)

        1.  登录 master 节点，创建 tls.key和tls.crt。

            ``` {#codeblock_nb2_ri3_wpx}
            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
            ```

        2.  执行 `vim tls.key` 和 `vim tls.crt` 获取生成的私钥和证书。
        3.  将证书和私钥的内容复制到 TLS 新建密钥的面板。
9.  添加标签。

    标签的作用是为Ingress添加对应的标签，标示该Ingress的特点。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156047881810783_zh-CN.png)

10. 最后单击**创建**，返回路由列表。

    等待一段时间，可以看到一条路由。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156047881810784_zh-CN.png)

11. 单击路由中的访问域名 `test.[cluster-id].[region-id].alicontainer.com`，以及 `foo.bar.com` ，可访问 nginx 的欢迎页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156047881810786_zh-CN.png)

    单击指向new-nginx服务的路由地址，发现指向了old-nginx应用的页面。

    **说明：** 在浏览器中访问路由地址，默认情况下，请求头（Header）中没有前面步骤中定义的`foo=^bar$`，因此流量会导向old-nginx应用。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16682/156047881910791_zh-CN.png)

12. SSH登录到Master节点，执行以下命令，模拟带有特定请求头的访问结果。

    ``` {#codeblock_xod_zbd_mc1}
    curl -H "Host: foo.bar.com" http://47.107.20.35
    old
     curl -H "Host: foo.bar.com" http://47.107.20.35
    old
     curl -H "Host: foo.bar.com" http://47.107.20.35                     #类似于浏览器的访问请求
    old
     curl -H "Host: foo.bar.com" -H "foo: bar" http://47.107.20.35           #模拟带有特有header的访问请求，会根据路由权重返回结果
    new
     curl -H "Host: foo.bar.com" -H "foo: bar" http://47.107.20.35
    old
     curl -H "Host: foo.bar.com" -H "foo: bar" http://47.107.20.35
    old
     curl -H "Host: foo.bar.com" -H "foo: bar" http://47.107.20.35
    new
    ```


