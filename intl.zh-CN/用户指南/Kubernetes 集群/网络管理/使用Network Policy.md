# 使用Network Policy {#concept_tym_gtb_wfb .concept}

## 前提条件 {#section_mjt_5tb_wfb .section}

-   您已经成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。

-   您在创建集群时，**网络插件**选择**Terway**，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。
-   您可以通过Kubectl连接到Kubernetes 集群，参见[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

## 测试nginx服务可以被其他pod正常访问 {#section_m2s_hyh_wfb .section}

1.  执行以下命令，创建一个nginx的应用，并通过名称为ngnix的Service将其暴露。

    ```
    $ kubectl run nginx --image=nginx
    deployment.apps/nginx created
    $ kubectl get pod
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx-64f497f8fd-znbxb   1/1     Running   0          45s
    
    $ kubectl expose deployment nginx --port=80
    service/nginx exposed
    $ kubectl get service
    NAME         TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
    kubernetes   ClusterIP   172.19.0.1    <none>        443/TCP   3h
    nginx        ClusterIP   172.19.8.48   <none>        80/TCP    10s
    ```

2.  执行以下命令，创建名称为busybox的Pod，访问步骤1创建的nginx服务。

    ```
    $ kubectl run busybox --rm -ti --image=busybox /bin/sh
    kubectl run --generator=deployment/apps.v1beta1 is DEPRECATED and will be removed in a future version. Use kubectl create instead.
    If you don't see a command prompt, try pressing enter.
    / # wget nginx
    Connecting to nginx (172.19.8.48:80)
    index.html           100% |***************************************************************************************************|   612  0:00:00 ETA
    / #
    ```


## 通过Network Policy限制服务只能被带有特定label的应用访问 {#section_sxs_n5b_wfb .section}

1.  执行以下命令，创建policy.yaml文件。

    ```
    $ vim policy.yaml
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
    $ kubectl apply -f policy.yaml 
    networkpolicy.networking.k8s.io/access-nginx created
    ```

3.  执行以下命令，当没有定义访问标签时，测试访问nginx服务，请求会超时，无法访问。

    ```
    $ kubectl run busybox --rm -ti --image=busybox /bin/sh
    If you don't see a command prompt, try pressing enter.
    / # wget nginx
    Connecting to nginx (172.19.8.48:80)
    wget: can't connect to remote host (172.19.8.48): Connection timed out
    / #
    ```

4.  执行以下命令，定义访问标签，测试访问nginx服务，请求成功，正常访问。

    ```
    $ kubectl run busybox --rm -ti --labels="access=true" --image=busybox /bin/sh
    If you don't see a command prompt, try pressing enter.
    / # wget nginx
    Connecting to nginx (172.19.8.48:80)
    index.html           100% |***************************************************************************************************|   612  0:00:00 ETA
    / #
    ```


## 通过Network Policy限制能够访问暴露了公网SLB服务的来源IP段 {#section_pdz_jyh_wfb .section}

1.  执行以下命令，为上述nginx应用创建阿里云负载均衡服务，指定 `type=LoadBalancer` 来向外网用户暴露 nginx 服务。

    ```
    $ vim nginx-service.yaml
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
    
    $ kubectl apply -f nginx-service.yaml 
    service/nginx-slb created
    
    $ kubectl get service nginx-slb
    NAME        TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE
    nginx-slb   LoadBalancer   172.19.12.254   47.110.200.119   80:32240/TCP   8m
    ```

2.  执行以下命令，访问刚刚创建的SLB的IP地址47.110.200.119，访问失败。

    ```
    $ wget 47.110.200.119
    --2018-11-21 11:46:05--  http://47.110.200.119/
    Connecting to 47.110.200.119:80... failed: Connection refused.
    ```

    **说明：** 

    访问失败的原因是：

    -   我们刚刚配置了nginx服务只能被带有特定label即`access=true`的应用访问。
    -   访问SLB的IP地址，是从外部访问Kubernetes，与[通过Network Policy限制服务只能被带有特定label的应用访问](#section_sxs_n5b_wfb)不同。
    解决方法：修改Network Policy，增加允许访问的来源IP地址段。

3.  执行以下命令，查看本地的IP地址。

    ```
    $ curl myip.ipip.net
     
    当前 IP：10.0.0.1 来自于：中国 北京 北京        #此处仅为示例，具体请以实际设备为准
    ```

4.  执行以下命令，修改已经创建的policy.yaml文件。

    ```
    $ vim policy.yaml
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
            cidr: 10.0.0.1/24      #本地IP地址，此处仅为示例，具体请以实际设备为准
    
    $ kubectl apply -f policy.yaml 
    networkpolicy.networking.k8s.io/access-nginx unchanged
    ```

    **说明：** 

    -   有些网络的出口有多个IP地址，这里请使用/24的地址范围。
    -   SLB健康检查地址在`100.64.0.0/10`地址段内，因此请务必配置`100.64.0.0/10`。
5.  执行以下命令，测试访问nginx服务，访问成功。

    ```
    $  kubectl run busybox --rm -ti --labels="access=true" --image=busybox /bin/sh
    
    If you don't see a command prompt, try pressing enter.
    / # wget 47.110.200.119
    Connecting to 47.110.200.119 (47.110.200.119:80)
    index.html           100% |***********************************************************|   612  0:00:00 ETA
    / #
    ```


## 通过Network Policy限制一个Pod只能访问www.aliyun.com {#section_lzz_s4j_wfb .section}

1.  执行以下命令，获取www.aliyun.com域名解析到的IP地址列表。

    ```
    $ dig +short www.aliyun.com
    www-jp-de-intl-adns.aliyun.com.
    www-jp-de-intl-adns.aliyun.com.gds.alibabadns.com.
    v6wagbridge.aliyun.com.
    v6wagbridge.aliyun.com.gds.alibabadns.com.
    106.11.93.21
    140.205.32.4
    140.205.230.13
    140.205.34.3
    ```

2.  执行以下命令，创建busybox-policy文件。

    ```
    $ vim busybox-policy.yaml
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
            cidr: 106.11.93.21/32
        - ipBlock:
            cidr: 140.205.32.4/32
        - ipBlock:
            cidr: 140.205.230.13/32
        - ipBlock:
            cidr: 140.205.34.3/32
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
    $ kubectl apply -f busybox-policy.yaml 
    networkpolicy.networking.k8s.io/busybox-policy created
    ```

4.  执行以下命令，访问www.aliyun.com之外的网站，例如：www.google.com，访问失败。

    ```
    $ kubectl run busybox --rm -ti --image=busybox /bin/sh
    If you don't see a command prompt, try pressing enter.
    / # wget www.google.com
    Connecting to www.google.com (64.13.192.74:80)
    wget: can't connect to remote host (64.13.192.74): Connection timed out
    ```

5.  执行以下命令，访问www.aliyun.com，访问成功。

    ```
    / # wget www.aliyun.com
    Connecting to www.aliyun.com (140.205.34.3:80)
    Connecting to www.aliyun.com (140.205.34.3:443)
    wget: note: TLS certificate validation not implemented
    index.html           100% |***********************************************************|  462k  0:00:00 ETA
    / #
    ```


