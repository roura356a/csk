---
keyword: [安全路由服务, Rewrite配置, 路由域名, HTTPS双向认证, 灰度发布, 申请HTTPS证书]
---

# Ingress高级用法

在Kubernetes集群中，Ingress对集群服务（Service）中外部可访问的API对象进行管理，提供七层负载均衡能力。您可以给Ingress配置提供外部可访问的URL、Rewrite配置、HTTPS服务、以及灰度发布功能等。本文介绍如何配置安全的路由服务、HTTPS双向认证、域名支持正则化及泛化，申请免费的HTTPS证书等功能。

-   已创建Kubernetes集群。具体操作步骤，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。
-   ACK集群中的Ingress Controller运行正常。
-   安装[kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)命令行工具。
-   已创建示例Deployment和Service。具体操作步骤，请参见[Kubectl操作指导](/intl.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/Ingress基本操作.md)。

## 配置说明

针对ingress-nginx-controller，我们采用与社区完全兼容的配置方式。关于所有的配置说明，请参见[NGINX Configuration](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/)。

目前其主要支持三种配置方式：

-   基于Annotation的方式：在每个Ingress YAML的Annotation里配置，仅仅只对本Ingress生效。更多信息，请参见[Annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/)。
-   基于ConfigMap的方式：通过kube-system/nginx-configuration configmap的配置，是一个全局的配置，对所有的Ingress生效。更多信息，请参见[ConfigMaps](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/)。
-   自定义NGINX Template模板的方式：对Ingress Controller内部的NGINX template有特殊配置要求，且当前通过Annotation和ConfgMap方式都无法满足诉求的情况下采用该方式。更多信息，请参见[Custom NGINX template](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/custom-template/)。

## 配置URL重定向的路由服务

通过以下命令创建一个简单的Ingress，所有对/svc路径的访问都会重新定向到后端服务能够识别的/路径上面。

1.  部署以下模板，创建Ingress。

    ```
    cat <<-EOF | kubectl apply -f -
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: rewrite-test-ingress
      namespace: default
      annotations:
        # URL重定向
        nginx.ingress.kubernetes.io/rewrite-target: /$1
    spec:
      rules:
      - host: rewrite-test-ingress.com
        http:
          paths:
          - path: /svc/(.*)
            backend:
              serviceName: web1-service
              servicePort: 80
    EOF
    ```

2.  执行以下命令，访问Nginx服务。

    替换**IP\_ADDRESS**为Ingress对应的IP，可通过`kubectl get ing`获取。

    ```
    curl -k -H "Host: rewrite-test-ingress.com"  http://{IP_ADDRESS}/svc/foo
    ```

    预期输出：

    ```
    web1: /foo
    ```


## Rewrite配置

使用`inginx.ingress.kubernetes.io/rewrite-target`注解支持基本的Rewrite配置，对于一些复杂高级的Rewrite需求，可以通过如下注解来实现：

-   `nginx.ingress.kubernetes.io/server-snippet`：扩展配置到Locaton章节。
-   `nginx.ingress.kubernetes.io/configuration-snippet`：扩展配置到Server章节。

配置示例：

```
annotations:
     nginx.ingress.kubernetes.io/server-snippet: |
         rewrite ^/v4/(.*)/card/query http://m.maizuo.com/v5/#!/card/query permanent;
     nginx.ingress.kubernetes.io/configuration-snippet: |
         rewrite ^/v6/(.*)/card/query http://m.maizuo.com/v7/#!/card/query permanent;
```

示例配置生成的nginx.conf如下所示。

```
## start server foo.bar.com
    server {
        server_name foo.bar.com ;
        listen 80;
        listen [::]:80;
        set $proxy_upstream_name "-";
    ### server-snippet配置。
        rewrite ^/v4/(.*)/card/query http://m.maizuo.com/v5/#!/card/query permanent;
        ...
    ### configuration-snippet配置。
      rewrite ^/v6/(.*)/card/query http://m.maizuo.com/v7/#!/card/query permanent;
      ...
    }
    ## end server foo.bar.com
```

同时，snippet也支持一些全局配置。详细信息，请参见[server-snippet](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#main-snippet)。

## 配置安全的路由服务

支持多证书管理，为您的服务提供安全防护。

1.  准备您的服务证书。

    如果没有证书，可以通过下面的方法生成测试证书。

    **说明：** 域名需要与您的Ingress配置保持一致。

    1.  执行以下命令，生成一个证书文件tls.crt和一个私钥文件tls.key。

        ```
        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
        ```

    2.  执行以下命令，创建密钥。

        通过该证书和私钥创建一个名为foo.bar的Kubernetes Secret。创建Ingress时需要引用这个Secret。

        ```
        kubectl create secret tls foo.bar --key tls.key --cert tls.crt
        ```

2.  执行以下命令，创建一个安全的Ingress服务。

    ```
    cat <<EOF | kubectl create -f - 
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: test-test-ingress
    spec:
        #安全路由
      tls:
      - hosts:
        - tls-test-ingress.com
        secretName: tls-test-ingress
      rules:
      - host: tls-test-ingress.com
        http:
          paths:
          - path: /foo
            backend:
              serviceName: web1-svc
              servicePort: 80
    EOF
    ```

3.  执行以下命令，查询Ingress信息。

    ```
    kubectl get ing
    ```

    预期输出：

    ```
    NAME                   HOSTS         ADDRESS          PORTS     AGE
    test-test-ingress      *             101.37.XX.XX     80        11s
    ```

4.  配置`hosts`文件或者设置域名来访问该TLS服务。

    您可以通过`http://tls-test-ingress.com/foo`访问到`web1-svc`服务。


## 配置HTTPS双向认证

某些业务场景需要启用HTTPS双向验证，Ingress-Nginx支持该特性，配置步骤参考以下示例。

1.  执行以下命令，创建自签的CA证书。

    ```
    openssl req -x509 -sha256 -newkey rsa:4096 -keyout ca.key -out ca.crt -days 356 -nodes -subj '/CN=Fern Cert Authority'
    ```

    预期输出：

    ```
    Generating a 4096 bit RSA private key
    .............................................................................................................++
    .....................................................................................++
    writing new private key to 'ca.key'
    ```

2.  执行以下命令，创建Server端证书。

    1.  执行以下命令，生成Server端证书的请求文件。

        ```
        openssl req -new -newkey rsa:4096 -keyout server.key -out server.csr -nodes -subj '/CN=test.nginx.ingress.com'
        ```

        预期输出：

        ```
        Generating a 4096 bit RSA private key
        ................................................................................................................................++
        .................................................................++
        writing new private key to 'server.key'
        ```

    2.  执行以下命令，使用根证书签发Server端请求文件，生成Server端证书。

        ```
        openssl x509 -req -sha256 -days 365 -in server.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out server.crt
        ```

        预期输出：

        ```
        Signature ok
        subject=/CN=test.nginx.ingress.com
        Getting CA Private Key
        ```

3.  执行以下命令，创建Client端证书。

    1.  生成Client端证书的请求文件。

        ```
        openssl req -new -newkey rsa:4096 -keyout client.key -out client.csr -nodes -subj '/CN=Fern'
        ```

        预期输出：

        ```
        Generating a 4096 bit RSA private key
        .......................................................................................................................................................................................++
        ..............................................++
        writing new private key to 'client.key'
        -----
        ```

    2.  执行以下命令，使用根证书签发Client端请求文件，生成Client端证书。

        ```
        openssl x509 -req -sha256 -days 365 -in client.csr -CA ca.crt -CAkey ca.key -set_serial 02 -out client.crt
        ```

        预期输出：

        ```
        Signature ok
        subject=/CN=Fern
        Getting CA Private Key
        ```

4.  执行以下命令，检查创建的证书。

    ```
    ls
    ```

    预期输出：

    ```
    ca.crt  ca.key  client.crt  client.csr  client.key  server.crt  server.csr  server.key
    ```

5.  执行以下命令，创建CA证书的Secret。

    ```
    kubectl create secret generic ca-secret --from-file=ca.crt=ca.crt
    ```

    预期输出：

    ```
    secret/ca-secret created
    ```

6.  执行以下命令，创建Server证书的Secret。

    ```
    kubectl create secret generic tls-secret --from-file=tls.crt=server.crt --from-file=tls.key=server.key
    ```

    预期输出：

    ```
    secret/tls-secret created
    ```

7.  执行以下命令，创建测试用的Ingress用例。

    ```
    cat <<-EOF | kubectl apply -f -
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      annotations:
        nginx.ingress.kubernetes.io/auth-tls-verify-client: "on"
        nginx.ingress.kubernetes.io/auth-tls-secret: "default/ca-secret"
        nginx.ingress.kubernetes.io/auth-tls-verify-depth: "1"
        nginx.ingress.kubernetes.io/auth-tls-pass-certificate-to-upstream: "true"
      name: nginx-test
      namespace: default
    spec:
      rules:
      - host: test.nginx.ingress.com
        http:
          paths:
          - backend:
              serviceName: http-svc
              servicePort: 80
            path: /
      tls:
      - hosts:
        - test.nginx.ingrss.com
        secretName: tls-secret
    EOF
    ```

    预期输出：

    ```
    ingress.networking.k8s.io/nginx-test configured
    ```

8.  执行以下命令，查看Ingress的IP地址。

    ```
    kubectl get ing
    ```

    预期输出如下，ADDRESS字段对应的IP地址即为Ingress的IP地址。

    ```
    NAME         HOSTS                    ADDRESS         PORTS     AGE
    nginx-test   test.nginx.ingress.com   39.102.xx.xx   80, 443   4h42m
    ```

9.  执行以下命令，更新Hosts文件，替换下面的IP地址为真实获取的Ingress的IP地址。

    ```
    sudo echo "39.102.xx.xx  test.nginx.ingress.com" >> /etc/hosts
    ```

    **结果验证：**

    -   客户端不传证书访问

        ```
        curl --cacert ./ca.crt  https://test.nginx.ingress.com
        ```

        预期输出：

        ```
        <html>
        <head><title>400 No required SSL certificate was sent</title></head>
        <body>
        <center><h1>400 Bad Request</h1></center>
        <center>No required SSL certificate was sent</center>
        <hr><center>nginx/1.19.0</center>
        </body>
        </html>
        ```

    -   客户端传证书访问

        ```
        curl --cacert ./ca.crt --cert ./client.crt --key ./client.key https://test.nginx.ingress.com
        ```

        预期输出：

        ```
        <!DOCTYPE html>
        <html>
        <head>
        <title>Welcome to nginx!</title>
        <style>
            body {
                width: 35em;
                margin: 0 auto;
                font-family: Tahoma, Verdana, Arial, sans-serif;
            }
        </style>
        </head>
        <body>
        <h1>Welcome to nginx!</h1>
        <p>If you see this page, the nginx web server is successfully installed and
        working. Further configuration is required.</p>
        
        <p>For online documentation and support please refer to
        <a href="http://nginx.org/">nginx.org</a>.<br/>
        Commercial support is available at
        <a href="http://nginx.com/">nginx.com</a>.</p>
        
        <p><em>Thank you for using nginx.</em></p>
        </body>
        </html>
        ```


## 配置HTTPS服务转发到后端容器为HTTPS协议

当后端业务容器内是HTTPS服务时，可以通过使用注解`nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"`来使得Ingress Controller转发到后端容器是仍为HTTPS协议。

Ingress配置示例如下：

```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: backend-https
  annotations:
    # 注意这里：必须指定后端服务为HTTPS服务。
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
spec:
  tls:
  - hosts:
    - your-host-name
    secretName: your-sercret-cert-name
  rules:
  - host: your-host-name
    http:
      paths:
      - path: /
        backend:
          serviceName: your-service-name
          servicePort: your-service-port
```

## 配置域名支持正则化

在Kubernetes集群中，Ingress资源不支持对域名配置正则表达式，但是可以通过`nginx.ingress.kubernetes.io/server-alias`注解来实现。

1.  创建Ingress，以正则表达式`~^www\.\d+\.example\.com`为例。

    ```
    cat <<-EOF | kubectl apply -f -
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: ingress-regex
      namespace: default
      annotations:
        nginx.ingress.kubernetes.io/server-alias: '~^www\.\d+\.example\.com$, abc.example.com'
    spec:
      rules:
      - host: foo.bar.com
        http:
          paths:
          - path: /foo
            backend:
              serviceName: http-svc1
              servicePort: 80
     EOF
    ```

2.  执行以下命令，查看对应Nginx Ingress Controller的配置。

    1.  执行以下命令，查看部署Nginx Ingress Controller服务的Pod。

        ```
        kubectl get pods -n kube-system | grep nginx-ingress-controller
        ```

        预期输出：

        ```
        nginx-ingress-controller-77cd987c4c-cp87g         1/1     Running   0          1h
        nginx-ingress-controller-77cd987c4c-xvpt5         1/1     Running   0          1h
        ```

    2.  执行以下命令，查看对应Nginx Ingress Controller的配置，可以发现生效的配置（Server\_Name字段）。

        ```
        kubectl exec -n kube-system nginx-ingress-controller-77cd987c4c-cp87g cat /etc/nginx/nginx.conf | grep -C3 "regex.ingress.test.com"
        ```

        预期输出：

        ```
          ## start server foo.bar.com
          server {
        --
          server {
            server_name foo.bar.com abc.example.com ~^www\.\d+\.example\.com$ ;
            listen 80  ;
            listen 443  ssl http2 ;
        --
        --
            }
          }
          ## end server foo.bar.com
        ```

3.  执行以下命令，获取Ingress对应的IP。

    ```
    kubectl get ing
    ```

    预期输出：

    ```
    NAME            HOSTS         ADDRESS          PORTS     AGE
    ingress-regex   foo.bar.com   101.37.XX.XX     80        11s
    ```

4.  执行以下命令，进行不同规则下的服务访问测试。

    配置以下**IP\_ADDRESS**为上一步获取的IP地址。

    -   执行以下命令，通过`Host: foo.bar.com`访问服务。

        ```
        curl -H "Host: foo.bar.com" {IP_ADDRESS}/foo
        ```

        预期输出：

        ```
        /foo
        ```

    -   执行以下命令，通过`Host: www.123.example.com`访问服务。

        ```
        curl -H "Host: www.123.example.com" {IP_ADDRESS}/foo
        ```

        预期输出：

        ```
        /foo
        ```

    -   执行以下命令，通过`Host: www.321.example.com`访问服务。

        ```
        curl -H "Host: www.321.example.com" {IP_ADDRESS}/foo
        ```

        预期输出：

        ```
        /foo
        ```


## 配置域名支持泛化

在Kubernetes集群中，Ingress资源支持对域名配置泛域名，例如可配置`*. ingress-regex.com`泛域名。

1.  部署以下模板，创建Ingress。

    ```
    $ cat <<-EOF | kubectl apply -f -
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: ingress-regex
      namespace: default
    spec:
      rules:
      - host: '*.ingress-regex.com'
        http:
          paths:
          - path: /foo
            backend:
              serviceName: http-svc1
              servicePort: 80
     EOF
    ```

2.  执行以下命令，查看对应Nginx Ingress Controller的配置，可以发现生效的配置（Server\_Name字段）。

    ```
    kubectl exec -n kube-system {ningx-ingress-pod-name} cat /etc/nginx/nginx.conf | grep -C3 "ingress-regex.com"
    ```

    **说明：** 替换ningx-ingress-pod-name为实际环境的nginx-ingress pod。

    预期输出：

    ```
    ## start server *.bar.com
      server {
        server_name *.ingress-regex.com ;
        listen 80;
        listen [::]:80;
    ...
      }
      ## end server *.bar.com
    ```

3.  执行以下命令，获取Ingress对应的IP。

    ```
    kubectl get ing
    ```

    预期输出：

    ```
    NAME            HOSTS         ADDRESS          PORTS     AGE
    ingress-regex   *.bar.com    101.37.XX.XX      80        11s
    ```

4.  执行以下命令，进行不同规则下的服务访问测试。

    配置以下**IP\_ADDRESS**为上一步获取的IP地址。

    -   执行以下命令，通过`Host: abc.ingress-regex.com`访问服务。

        ```
        curl -H "Host: abc.ingress-regex.com" {IP_ADDRESS}/foo
        ```

        预期输出：

        ```
        /foo
        ```

    -   执行以下命令，通过`Host: 123.ingress-regex.com`访问服务。

        ```
        curl -H "Host: 123.ingress-regex.com" {IP_ADDRESS}/foo
        ```

        预期输出：

        ```
        /foo
        ```

    -   执行以下命令，通过`Host: a1b1.ingress-regex.com`访问服务。

        ```
        curl -H "Host: a1b1.ingress-regex.com" {IP_ADDRESS}/foo
        ```

        预期输出：

        ```
        /foo
        ```


## 通过注解实现灰度发布

灰度发布功能可以通过设置注解来实现，为了启用灰度发布功能，需要设置注解`nginx.ingress.kubernetes.io/canary: "true"` , 通过不同注解可以实现不同的灰度发布功能：

-   `nginx.ingress.kubernetes.io/canary-weight`：设置请求到指定服务的百分比（值为0~100的整数）。
-   `nginx.ingress.kubernetes.io/canary-by-header`：基于request header的流量切分，当配置的`hearder`值为always时，请求流量会被分配到灰度服务入口；当`hearder`值为never时，请求流量不会分配到灰度服务；将忽略其他hearder值，并通过灰度优先级将请求流量分配到其他规则设置的灰度服务。
-   `nginx.ingress.kubernetes.io/canary-by-header-value`和`nginx.ingress.kubernetes.io/canary-by-header`：当请求中的`hearder`和`header-value`与设置的值匹配时，请求流量会被分配到灰度服务入口；将忽略其他hearder值，并通过灰度优先级将请求流量分配到其他规则设置的灰度服务。
-   `nginx.ingress.kubernetes.io/canary-by-cookie`：基于cookie的流量切分，当配置的`cookie`值为always时，请求流量将被分配到灰度服务入口；当配置的`cookie`值为never时，请求流量将不会分配到灰度服务入口。

不同注解配置示例如下：

-   基于权重灰度：配置灰度服务的权重为20%。

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      annotations:
        kubernetes.io/ingress.class: nginx
        nginx.ingress.kubernetes.io/canary: "true"
        nginx.ingress.kubernetes.io/canary-weight: "20"
    ```

-   基于header灰度：请求header为 `ack：always`时将访问灰度服务；请求header为 `ack：never`时将不访问灰度服务；其它header将根据灰度权重将流量分配给灰度服务。

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      annotations:
        kubernetes.io/ingress.class: nginx
        nginx.ingress.kubernetes.io/canary: "true"
        nginx.ingress.kubernetes.io/canary-weight: "50"
        nginx.ingress.kubernetes.io/canary-by-header: "ack"
    ```

-   基于header灰度（自定义header值）：当请求header为`ack: alibaba`时将访问灰度服务；其它header将根据灰度权重将流量分配给灰度服务。

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      annotations:
        kubernetes.io/ingress.class: nginx
        nginx.ingress.kubernetes.io/canary: "true"
        nginx.ingress.kubernetes.io/canary-weight: "20"
        nginx.ingress.kubernetes.io/canary-by-header: "ack"
        nginx.ingress.kubernetes.io/canary-by-header-value: "alibaba"
    ```

-   基于cookie灰度：当header不匹配时，请求的cookie为`hangzhou_region=always`时将访问灰度服务。

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      annotations:
        kubernetes.io/ingress.class: nginx
        nginx.ingress.kubernetes.io/canary: "true"
        nginx.ingress.kubernetes.io/canary-weight: "20"
        nginx.ingress.kubernetes.io/canary-by-header: "ack"
        nginx.ingress.kubernetes.io/canary-by-header-value: "alibaba"
        nginx.ingress.kubernetes.io/canary-by-cookie: "hangzhou_region"
    ```


**说明：**

-   基于cookie的灰度不支持设置自定义，只有always和never。
-   灰度优先级顺序：基于header \> 基于cookie \> 基于权重（从高到低）。

## 使用cert-manager申请免费的HTTPS证书

cert-manager是一个云原生证书管理开源工具，用于在Kubernetes集群中提供HTTPS证书并自动续期。以下示例介绍了如何使用cert-manager自动申请免费证书并自动续期。

1.  执行以下命令，部署cert-manager。

    ```
    kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.1.0/cert-manager.yaml
    ```

2.  执行以下命令，查看Pod状态。

    ```
    kubectl get pods -n cert-manager
    ```

    预期输出：

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    cert-manager-1           1/1     Running   0          2m11s
    cert-manager-cainjector  1/1     Running   0          2m11s
    cert-manager-webhook     1/1     Running   0          2m10s
    ```

3.  执行以下命令，创建ClusterIssuer。

    ```
    cat <<EOF | kubectl apply -f -
    apiVersion: cert-manager.io/v1
    kind: ClusterIssuer
    metadata:
      name: letsencrypt-prod-http01
    spec:
      acme:
        server: https://acme-v02.api.letsencrypt.org/directory
        email: your_email_name@gmail.com  #替换为您的邮箱名。
        privateKeySecretRef:
          name: letsencrypt-http01
        solvers:
        - http01: 
            ingress:
              class: nginx
    EOF
    ```

4.  执行以下命令，查看ClusterIssuer。

    ```
    kubectl get clusterissuer
    ```

    预期输出：

    ```
    NAME                         READY   AGE
    letsencrypt-prod-http01      True    17s
    ```

5.  执行以下命令，创建Ingress资源对象。

    ```
    cat <<EOF | kubectl apply -f -
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: ingress-tls
      annotations:
        kubernetes.io/ingress.class: "nginx"
        cert-manager.io/cluster-issuer: "letsencrypt-prod-http01"
    spec:
      tls:
      - hosts:
        - your_domain_name        # 替换为您的域名。
        secretName: ingress-tls   
      rules:
      - host: your_domain_name    # 替换为您的域名。
        http:
          paths:
          - path: /
            backend:
              serviceName: your_service_name  # 替换为您的后端服务名。
              servicePort: your_service_port  # 替换为您的后端服务名。
    EOF
    ```

    **说明：** 替换的域名your\_domain\_name必须符合以下条件：

    -   域名不能超过64个字符。
    -   不支持泛域名。
    -   在公网以HTTP协议可正常访问。
6.  执行以下命令，查看证书。

    ```
    kubectl get cert
    ```

    预期输出：

    ```
    NAME          READY   SECRET     AGE
    ingress-tls   True    ingress-tls   52m
    ```

    **说明：** 如果**READY**状态不为**True**，可通过`kubect describe cert ingress-tls`查看证书处理过程。

7.  执行以下命令，查看Secret。

    ```
    kubectl get secret  ingress-tls
    ```

    预期输出：

    ```
    NAME          TYPE                DATA   AGE
    ingress-tls   kubernetes.io/tls   2      2m
    ```

8.  通过Web浏览器输入`https:[网站域名]`访问设置的域名。


