# 自定义Istio网关 {#concept_183093 .concept}

本文提供了部署Istio自定义入口网关的步骤说明，以及如何使用cert-manager进行证书管理。

Istio Gateway提供多个自定义入口网关的支持能力，通过开放一系列端口用于承载网格边缘的进入连接，同时可以使用不同loadbalancer来隔离不同的入口流量。cert-manager可用于使用存储在Kubernetes Secret资源中的任意签名密钥对来获取证书。

## 前提条件 {#section_nl8_3hy_xl5 .section}

-   Kubernetes+Istio环境已部署完成。

    阿里云容器服务Kubernetes目前已经支持 Istio的一键部署，可以通过容器服务管理控制台快速[创建Kubernetes集群](../../../../intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes 集群.md#)以及[部署Istio](../../../../intl.zh-CN/Kubernetes集群用户指南/Istio管理/部署Istio.md#)。

-   通过[CloudShell](../../../../intl.zh-CN/Kubernetes集群用户指南/集群管理/管理与访问集群/在CloudShell上通过kubectl管理Kubernetes集群.md#) 连接 Kubernetes 集群。
-   为简化管理，要保证网关的名称与创建的secretName保持统一，具体来说，secretName的名称应该为istio-\{网关名称\}-certs或者istio-\{网关名称\}-ca-cert。

## 启用cert-manager {#section_18q_z58_hck .section}

1.  在Istio默认安装时的配置中，没有启用cert-manager进行证书管理。可以通过更新配置方式，在Istio安装之后重新启用cert-manager。登录 [容器服务管理控制台](https://cs.console.aliyun.com/)
2.  在 Kubernetes 菜单下，单击左侧导航栏的服务网格 \> Istio管理，进入管理页面。
3.  点击页面右上方的更新按钮，在弹出的对话框中，对Istio的参数进行编辑后，设置certManager.enabled值为true，然后单击更新。注意：如果不存在certManager配置项，可以在第一行添加，同时保持yaml格式。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/156901/156629529756583_zh-CN.png)


## 生成签名密钥对 {#section_u3o_nyf_sev .section}

CA Issuer不会自动创建和管理签名密钥对。可以通过以下任意一种方法获取：

-   如果您已经知道签名密钥对，需要将密钥命名为ca.key，证书命名为ca.crt。
-   如果您并不知道签名密钥对，您可以通过如下操作生成x509类型的密钥和证书。
    1.  执行如下命令，生成CA密钥。

        ``` {#codeblock_njc_u22_3xp}
        $ docker run -it -v $(pwd):/export frapsoft/openssl genrsa -out /export/ca.key 2048
        ```

    2.  执行如下命令，生成CA的自签名证书。

        ``` {#codeblock_s3i_vqx_7ca}
        $ docker run -it -v $(pwd):/export frapsoft/openssl req -x509 -new -nodes -key /export/ca.key -subj "/CN=myexample.com" -days 3650 -reqexts v3_req -extensions v3_ca -out /export/ca.crt
        ```


## 将签名密钥对保存为Secret {#section_iyy_4ck_svn .section}

通过以下命令将在默认命名空间中创建包含签名密钥对的Secret：

``` {#codeblock_ly6_0q3_hyk}
kubectl create namespace myexample
kubectl create secret tls istio-myexample-customingressgateway-ca-certs \
   --cert=ca.crt \
   --key=ca.key \
   --namespace=myexample
```

**说明：** 

-   创建一个使用此密钥对生成签名证书的颁发者Issuer，为了确保颁发者Issuer可以引用到上述密钥对，我们需要将其存储在Kubernetes Secret资源中。
-   颁发者Issuer是命名空间资源，因而只能在自己的命名空间中引用Secrets。因此，将密钥对放入与Issuer相同的名称空间中。

## 创建引用Secret的Issuer {#section_xhy_0oo_o34 .section}

创建并拷贝以下内容到certmanager-secret-issuer.yaml文件中，并执行`kubectl apply -n myexample -f certmanager-secret-issuer.yaml`命令，创建一个使用此密钥对生成签名证书的颁发者Issuer。

``` {#codeblock_t3g_64j_fui}
apiVersion: certmanager.k8s.io/v1alpha1
kind: Issuer
metadata:
  name: ca-issuer
  namespace: myexample
spec:
  ca:
    secretName: istio-myexample-customingressgateway-ca-certs
```

## 获得签名证书 {#section_xno_hmg_k5v .section}

现在可以创建以下证书资源，该资源指定所需的证书。为了使用Issuer获取证书，您必须在与Issuer相同的命名空间中创建Certificate资源，因为Issuer是命名空间资源，如本例所示。如果您想要跨多个名称空间重用签名密钥对，那么就可以使用一个集群ClusterIssuer。首先通过以下命令为域名myexample.com创建证书：

创建并拷贝以下内容到myexample-certificate.yaml文件中，并执行`kubectl apply -n myexample -f myexample-certificate.yaml`命令，创建签名证书。

``` {#codeblock_m3d_36b_428}
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: myexample-certificate
  namespace: myexample
spec:
  secretName: istio-myexample-customingressgateway-certs
  issuerRef:
    name: ca-issuer
    # 可以通过引用ClusterIssuer类型的颁发者Issuer；默认情况使用只适用于命名空间的Issuer
    kind: Issuer
  commonName: myexample.com
  organization:
  - MyExample CA
  dnsNames:
  - myexample.com
  - www.myexample.com
```

创建证书资源后，cert-manager将尝试使用颁发者ca-issuer获取证书。如果成功，证书将存储在与证书资源相同的命名空间myexample下的istio-myexample-customingressgateway-certs中。

## 检查证书与密钥 {#section_90f_l0z_qcz .section}

由于我们已指定commonName字段，因此myexample.com将是证书的通用名称，并且通用名称和dnsNames阵列的所有元素都将是主题备用名称 （SAN）。如果我们没有指定公共名称，那么dnsNames列表的第一个元素将用作公共名称，dnsNames列表的所有元素也将是SAN。

创建上述证书后，可以通过执行如下命令检查是否已成功获取。

如下所示查看了证书myexample-certificate：

``` {#codeblock_lqt_qlx_qun}
kubectl describe certificate myexample-certificate
Name:         myexample-certificate
Namespace:    default
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"certmanager.k8s.io/v1alpha1","kind":"Certificate","metadata":{"annotations":{},"name":"myexample-certificate","namespace":"...
API Version:  certmanager.k8s.io/v1alpha1
Kind:         Certificate
....
Spec:
  Common Name:  myexample.com
  Dns Names:
    myexample.com
    www.myexample.com
  Issuer Ref:
    Kind:  Issuer
    Name:  ca-issuer
  Organization:
    MyExample CA
  Secret Name:  istio-myexample-customingressgateway-certs
Status:
  Conditions:
    ....
    Message:               Certificate is up to date and has not expired
    Reason:                Ready
    Status:                True
    Type:                  Ready
....
Events:
  Type    Reason      Age   From          Message
  ----    ------      ----  ----          -------
  Normal  CertIssued  78s   cert-manager  Certificate issued successfully
			
```

当最后一行出现`Certificate issued successfully`时，表示证书创建成功。

您还可以执行`kubectl get secret istio-myexample-customingressgateway-certs -o yaml`，检查Issuer是否成功，如果成功，可以看到base64编码的签名TLS密钥对。

获得证书后，cert-manager将继续检查其有效性，并在接近到期时尝试更新。例如，证书上的Not After字段值a，当a＜当前时间+30天时，cert-manager认为证书即将到期。对于基于CA的颁发者，cert-manager将颁发证书，其中Not After字段设置为当前时间+365天。

## 部署自定义网关 {#section_vte_8bc_5ub .section}

Gateway描述了在网格边缘操作的负载均衡器，用于接收传入或传出的 HTTP/TCP 连接。阿里云容器服务Kubernetes提供了通过CRD方式管理自定义网关的支持，如下所示是一个自定义入口网关的YAML内容：

``` {#codeblock_p10_b9x_jeg}
apiVersion: istio.alibabacloud.com/v1beta1
kind: IstioGateway
metadata:  
  labels:    
    controller-tools.k8s.io: "1.0"  
  name: "myexample-customingressgateway"  
spec:  
  maxReplicas: 5  
  minReplicas: 1  
  ports:  
  - name: status-port    
    port: 15020    
    targetPort: 15020  
  - name: http2    
    port: 80    
    targetPort: 80  
  - name: https    
    port: 443    
    targetPort: 0  
  - name: tls    
    port: 15443    
    targetPort: 15443  
  replicaCount: 1  
  serviceType: LoadBalancer  
  serviceAnnotations:    
    service.beta.kubernetes.io/alicloud-loadbalancer-address-type: internet  
```

 **此处要保证网关的名称与上述创建的secretName保持统一，以简化管理，具体来说，secretName的名称应该为istio-\{网关名称\}-certs或者istio-\{网关名称\}-ca-certs。** 

创建并拷贝以上内容到myexample-customingressgateway.yaml文件中，并执行`kubectl apply -n myexample -f myexample-customingressgateway.yaml`命令，创建签名证书。

## 定义内部服务 {#section_1xl_yqk_hmb .section}

本示例中的内部服务是基于nginx实现的，首先为 NGINX 服务器创建配置文件。以域名myexample.com的内部服务为例，定义请求根路径直接返回字样Welcome to myexample.com! This is one custom Istio Ingress Gateway powered by cert-manager!及状态码200。

myexample-nginx.conf的具体内容如下：

``` {#codeblock_1nb_nwg_pbl}
events {
}

http {
  log_format main '$remote_addr - $remote_user [$time_local]  $status '
  '"$request" $body_bytes_sent "$http_referer" '
  '"$http_user_agent" "$http_x_forwarded_for"';
  access_log /var/log/nginx/access.log main;
  error_log  /var/log/nginx/error.log;

  server {
    listen 80;

    location / {
        return 200 'Welcome to myexample.com! This is one custom Istio Ingress Gateway powered by cert-manager!';
        add_header Content-Type text/plain;
    }
  }
}
```

1.  执行如下命令，创建 Kubernetes ConfigMap 存储 Nginx 服务器的配置。

    ``` {#codeblock_fwe_9lk_41x}
    kubectl create configmap myexample-nginx-configmap --from-file=nginx.conf=./myexample-nginx.conf
    ```

2.  执行如下命令，设置命名空间default，启用sidecar自动注入。

    ``` {#codeblock_npo_jie_kp4}
    kubectl label namespace default istio-injection=enabled
    ```

    **说明：** 确保该sidecar自动注入的Label需要在IngressGateway创建之后再进行标注，以确保IngressGateway不会自动注入。或者不启用自动注入，通过手工注入完成，具体参见[手工注入](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/#manual-sidecar-injection)。

3.  创建并拷贝如下内容到myexampleapp.yaml文件中，并执行`kubectl apply -f myexampleapp.yaml`命令，创建域名myexample.com的内部服务。

    ``` {#codeblock_hza_vud_pt8}
    apiVersion: v1
    kind: Service
    metadata:
      name: myexampleapp
      labels:
        app: myexampleapp
    spec:
      ports:
      - port: 80
        protocol: TCP
      selector:
        app: myexampleapp
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: myexampleapp
    spec:
      selector:
        matchLabels:
          app: myexampleapp
      replicas: 1
      template:
        metadata:
          labels:
            app: myexampleapp
        spec:
          containers:
          - name: nginx
            image: nginx
            ports:
            - containerPort: 80
            volumeMounts:
            - name: nginx-config
              mountPath: /etc/nginx
              readOnly: true
          volumes:
          - name: nginx-config
            configMap:
              name: myexample-nginx-configmap
    
    EOF
    ```


## 创建自定义网关配置对象 {#section_n8h_xlo_mih .section}

创建并拷贝如下内容到istio-myexample-customingressgateway.yaml文件中，并执行`kubectl apply -n myexample -f istio-myexample-customingressgateway.yaml`命令，创建Istio自定义网关配置对象。

``` {#codeblock_9ic_rke_k5k}
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  annotations:
  name: istio-myexample-customingressgateway
spec:
  selector:
    istio: ingressgateway
  servers:
  - hosts:
    - '*.myexample.com'
    port:
      name: http
      number: 80
      protocol: HTTP
    tls:
      httpsRedirect: true
  - hosts:
    - '*.myexample.com'
    port:
      name: https
      number: 443
      protocol: HTTPS
    tls:
      mode: SIMPLE
      privateKey: /etc/istio/myexample-customingressgateway-certs/tls.key
      serverCertificate: /etc/istio/myexample-customingressgateway-certs/tls.crt
```

## 创建VirtualService {#section_ymd_mxd_fa5 .section}

创建并拷贝如下内容到istio-myexample-customvirtualservice.yaml文件中，并执行`kubectl apply -n myexample -f istio-myexample-customvirtualservice.yaml`命令，创建链接到 istio-myexample-customingressgateway 的 VirtualService。

``` {#codeblock_u68_obl_391}
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: istio-myexample-customvirtualservice
spec:
  hosts:
  - "www.myexample.com"
  gateways:
  - istio-myexample-customingressgateway
  http:
  - route:
    - destination:
        host: myexampleapp.default.svc.cluster.local
        port:
          number: 80
				
```

## 通过网关访问服务 {#section_2uw_uv9_ii0 .section}

1.  以域名myexample.com为例，执行`kubectl get svc -n myexample -l istio=myexample-customingressgateway`命令，获取对应的自定义网关服务的公网IP地址。

    ``` {#codeblock_w54_chb_1xh}
    NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
    istio-myexample-customingressgateway   LoadBalancer   172.19.XX.XX   106.14.XX.XX    ....   11m
    ```

    **说明：** 将环境变量 INGRESS\_HOST 和SECURE\_INGRESS\_PORT这两个的取值替换成实际环境的地址值，即步骤1中查到的EXTERNAL-IP的值。

    ``` {#codeblock_9vj_cf2_a0a}
    INGRESS_HOST=106.14.XX.XX
    SECURE_INGRESS_PORT=443
    ```

2.  执行如下命令，检查 istio-ingressgateway Pod 是否正确的加载了证书和私钥。

    ``` {#codeblock_vwg_6kt_tx1}
    kubectl exec -it -n myexample $(kubectl -n myexample get pods -l istio=myexample-customingressgateway -o jsonpath='{.items[0].metadata.name}') -- ls -al /etc/istio/myexample-customingressgateway-certs
    ```

    此时，tls.crt 和 tls.key 都应该保存在这个目录中。

3.  执行如下命令，检查 Ingress gateway 证书中的 Subject 字段的正确性。

    ``` {#codeblock_xiv_c8q_2th}
    kubectl exec -i -n myexample $(kubectl get pod -l istio=myexample-customingressgateway  -n myexample -o jsonpath='{.items[0].metadata.name}')  -- cat /etc/istio/myexample-customingressgateway-certs/tls.crt | openssl x509 -text -noout | grep 'Subject:'
            Subject: O=MyExample CA, CN=myexample.com
    ```

4.  执行如下命令，检查 Ingress gateway 的代理能够正确访问证书。

    ``` {#codeblock_88z_yf7_0r3}
    kubectl exec -ti $(kubectl get po -l istio=myexample-customingressgateway -n myexample -o jsonpath={.items[0]..metadata.name}) -n myexample -- curl  127.0.0.1:15000/certs
    {
        "ca_cert": "",
        "cert_chain": "...."
    }
    ```


此时，使用cert-manager部署自定义入口网关的所有步骤已完成。

## 预期结果 {#section_af6_h1g_lje .section}

执行如下命令，通过 HTTPS 协议访问 myexample.com 服务。

``` {#codeblock_pmy_q5o_hyo}
curl -k -H Host:www.myexample.com --resolve www.myexample.com:443:106.14.XX.XX  https://www.myexample.com
Welcome to myexample.com! This is one custom Istio Ingress Gateway powered by cert-manager!
```

