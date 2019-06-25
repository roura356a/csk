# 基于istio的VirtualService和Destination完成蓝绿和灰度发布 {#concept_263233 .concept}

本文详细讲述如何通过istio的VirtualService和Destination配合来完成蓝绿和灰度发布。

## 环境准备 {#section_exb_17k_r9n .section}

-   部署 VirtualService 和Destination
    1.  通过[CloudShell](../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/在CloudShell上通过kubectl管理Kubernetes集群.md#) 连接 Kubernetes 集群。
    2.  创建并拷贝如下内容到virtual-service.yaml文件中，并执行`kubectl apply -f virtual-service.yaml`命令，部署 VirtualService。

        ``` {#codeblock_6dl_ckv_gtp}
        apiVersion: networking.istio.io/v1alpha3
        kind: VirtualService
        metadata:
          name: productpage
        spec:
          hosts:
          - productpage
          http:
          - route:
            - destination:
                host: productpage
                subset: v1
        ---
        apiVersion: networking.istio.io/v1alpha3
        kind: VirtualService
        metadata:
          name: reviews
        spec:
          hosts:
          - reviews
          http:
          - route:
            - destination:
                host: reviews
                subset: v1
        ---
        apiVersion: networking.istio.io/v1alpha3
        kind: VirtualService
        metadata:
          name: ratings
        spec:
          hosts:
          - ratings
          http:
          - route:
            - destination:
                host: ratings
                subset: v1
        ---
        apiVersion: networking.istio.io/v1alpha3
        kind: VirtualService
        metadata:
          name: details
        spec:
          hosts:
          - details
          http:
          - route:
            - destination:
                host: details
                subset: v1
        ```

    3.  创建并拷贝如下内容到destination.yaml文件中，并执行`kubectl apply -f destination.yaml`命令，部署 Destination。

        ``` {#codeblock_7ad_fdm_gsn}
        apiVersion: networking.istio.io/v1alpha3
        kind: DestinationRule
        metadata:
          name: productpage
        spec:
          host: productpage
          subsets:
          - name: v1
            labels:
              version: v1
        ---
        apiVersion: networking.istio.io/v1alpha3
        kind: DestinationRule
        metadata:
          name: reviews
        spec:
          host: reviews
          subsets:
          - name: v1
            labels:
              version: v1
          - name: v2
            labels:
              version: v2
          - name: v3
            labels:
              version: v3
        ---
        apiVersion: networking.istio.io/v1alpha3
        kind: DestinationRule
        metadata:
          name: ratings
        spec:
          host: ratings
          subsets:
          - name: v1
            labels:
              version: v1
          - name: v2
            labels:
              version: v2
          - name: v2-mysql
            labels:
              version: v2-mysql
          - name: v2-mysql-vm
            labels:
              version: v2-mysql-vm
        ---
        apiVersion: networking.istio.io/v1alpha3
        kind: DestinationRule
        metadata:
          name: details
        spec:
          host: details
          subsets:
          - name: v1
            labels:
              version: v1
          - name: v2
            labels:
              version: v2
        ```

-   设置地址转发

    ``` {#codeblock_nrc_psd_zu8}
    kubectl port-forward -n istio-system svc/istio-ingressgateway 3000:80
    ```

    此时，浏览器访问localhost:3000/productpage，可以看到book reviews一栏只有留言，没有评分，productpage使用的是v1版本的reviews服务。


## 蓝绿发布v2版本 {#section_v2o_yqz_plq .section}

经过前面的部署，reviews的v2版本已经在运行，现在要做的只是用蓝绿部署的方式，让v2版本上线即可。

本例中，部署完成后刷新页面，可以看到book reviews已经有了评分，而且评分的星星为黑色。

``` {#codeblock_1ty_tzd_stz}
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v2
```

## 按比例灰度发布v3版本 {#section_k84_sxy_480 .section}

您可以让v2，v3版本同时在线，且两个版本各处理50%的流量。

本例中，部署完成后，多次刷新页面，可以看到book reviews一栏v2，v3版本随机出现，v3版本的评分中实心星星为红色。

``` {#codeblock_xgn_5v6_zl1}
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
    - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v2
      weight: 50
    - destination:
        host: reviews
        subset: v3
      weight: 50
```

## 按http header灰度发布v3版本 {#section_8gf_xxh_xjh .section}

只按照流量来简单的灰度还是有很多场景不能满足，您还可以按照用户来灰度，不同的用户访问不同的页面。

本例中，部署完成后，多次刷新页面，评分区的星星始终为黑色。

``` {#codeblock_ud9_zzk_epb}
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - match:
    - headers:
        end-user:
          exact: jason
    route:
    - destination:
        host: reviews
        subset: v3
  - route:
    - destination:
        host: reviews
        subset: v2
```

此时，您可以单击右上角**Sign in**，以用户名 **jason**登录（不需要输入密码），您可以看到评分区的星星已经变成红色。

**说明：** 登录并访问后端服务时，会带上`end-user=XXX`的http header，使用**jason**登录后，就匹配到了上面yaml的规则，使得流量被引导到了v3版本的reviews。

