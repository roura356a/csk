# 使用网络策略（Network Policy）

本文介绍如何使用阿里云容器服务Kubernetes版集群的网络策略（Network Policy）。

您已完成以下操作：

-   [创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

    **说明：** 您在创建集群时，已设置**网络插件**为**Terway**。

-   [通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

## 测试Nginx服务可以被其他Pod正常访问

1.  执行以下命令，创建一个Nginx的应用，并通过名称为Ngnix的Service将其暴露。

    ```
    kubectl run nginx --image=nginx
    ```

    预期输出：

    ```
    deployment.apps/nginx created
    ```

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx-64f497f8fd-z****   1/1     Running   0          45s
    ```

    ```
    kubectl expose deployment nginx --port=80
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
    kubernetes   ClusterIP   172.XX.XX.1     <none>        443/TCP   3h
    nginx        ClusterIP   172.XX.XX.48    <none>        80/TCP    10s
    ```

2.  执行以下命令，创建名称为Busybox的Pod，访问[步骤1](#step_av6_77t_0ze)创建的Nginx服务。

    ```
    kubectl run busybox --rm -ti --image=busybox /bin/sh
    ```

    预期输出：

    ```
    kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl create instead.
    If you don't see a command prompt, try pressing enter.
    ```

    ```
    wget nginx
    ```

    预期输出：

    ```
    Connecting to nginx (172.XX.XX.48:80)
    index.html           100% |***************************************************************************************************|   612  0:00:00 ETA
    ```


## 通过Network Policy限制服务只能被带有特定label的应用访问

1.  执行以下命令，创建policy.yaml文件。

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
    ```

2.  执行以下命令，根据步骤1的policy.yaml文件创建Network Policy。

    ```
    kubectl apply -f policy.yaml 
    ```

    预期输出：

    ```
    networkpolicy.networking.k8s.io/access-nginx created
    ```

3.  执行以下命令，当没有定义访问标签时，测试访问Nginx服务，请求会超时，无法访问。

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

4.  执行以下命令，定义访问标签，测试访问Nginx服务。

    ```
    kubectl run busybox --rm -ti --labels="access=true" --image=busybox /bin/sh
    ```

    ```
    wget nginx
    ```

    预期输出：

    ```
    Connecting to nginx (172.19.8.48:80)
    index.html           100% |***************************************************************************************************|   612  0:00:00 ETA
    ```

    连接Nginx的进度为100%时，说明请求成功，可以正常访问Nginx服务。


## 通过Network Policy限制能够访问暴露了公网SLB服务的来源IP段

1.  执行以下命令，为上述Nginx应用创建阿里云SLB服务，指定`type=LoadBalancer`来向外网用户暴露Nginx服务。

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

    -   配置的nginx服务只能被带有特定label即`access=true`的应用访问。
    -   访问SLB的IP地址，是从外部访问Kubernetes，与通过Network Policy限制服务只能被带有特定Label的应用访问不同。
    解决方法：修改Network Policy，增加允许访问的来源IP地址段。

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


## 通过Network Policy限制一个Pod只能访问www.aliyun.com

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
        ports:
        - protocol: UDP
          port: 53
    ```

    **说明：** 在busybox-policy文件中，配置了Egress，即出网规则，限制应用的对外访问。在这里需配置允许UDP请求，否则无法做DNS解析。

3.  执行以下命令，根据busybox-policy文件创建Network Policy。

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


## 通过Network Policy控制Namespace下Pod公网访问权限

1.  执行以下命令，创建一个测试的Namespace来验证限制的能力。

    创建一个test-np的Namespace。

    ```
    kubectl create ns test-np
    ```

    预期输出：

    ```
    namespace/test-np created
    ```

2.  执行以下命令，为这个Namespace设置默认的只允许主动访问内网的Network Policy规则。

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

3.  执行以下命令，为特殊Label的Pod允许访问公网。

    示例Label设置为`public-network=true`。

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

4.  执行以下命令验证无特殊Label的Pod不能访问公网。

    ```
    kubectl run -it --namespace test-np --rm --image busybox  busybox-intranet
    ```

    ```
    ping aliyun.com
    ```

    预期输出：

    ```
    PING aliyun.com (106.11.248.146): 56 data bytes
    ^C
    --- aliyun.com ping statistics ---
    9 packets transmitted, 0 packets received, 100% packet loss
    ```

    显示0 packets received时，说明访问失败。

    **说明：** 访问失败的原因是：由于通过deny-public-net的Network Policy规则限制了test-np的这个命名空间下的Pod的默认的公网访问，所以它下面启动的默认label的Pod无法访问公网。

5.  执行以下命令验证带有public-network=true

    ```
    kubectl run -it --namespace test-np --labels public-network=true --rm --image busybox  busybox-internet
    ```

    ```
    ping aliyun.com
    ```

    预期输出：

    ```
    PING aliyun.com (106.11.172.56): 56 data bytes
    64 bytes from 106.11.172.56: seq=0 ttl=47 time=4.235 ms
    64 bytes from 106.11.172.56: seq=1 ttl=47 time=4.200 ms
    64 bytes from 106.11.172.56: seq=2 ttl=47 time=4.182 ms
    ^C
    --- aliyun.com ping statistics ---
    3 packets transmitted, 3 packets received, 0% packet loss
    round-trip min/avg/max = 4.182/4.205/4.235 ms
    ```

    显示0% packet loss时，说明成功访问服务。

    **说明：** 可以正常访问的原因是：由于在allow-public-network-for-labels的Network Policy的规则中允许了带有public-network=true的Pod的公网访问，所以在带有这个Label的busybox-internet的Pod可以访问公网。


