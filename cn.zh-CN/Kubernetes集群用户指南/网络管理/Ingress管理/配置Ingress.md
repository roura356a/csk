---
keyword: [配置路由服务, 路由域名, 安全路由服务]
---

# 配置Ingress

在Kubernetes集群中，Ingress是授权入站连接到达集群服务的规则集合，为您提供七层负载均衡能力。您可以给Ingress配置提供外部可访问的URL、负载均衡、SSL、基于名称的虚拟主机等。本文介绍如何配置简单的路由服务、路由默认域名、安全的路由服务等。

为了测试复杂路由服务，本例中创建一个Nginx的示例应用，您需要事先创建Nginx的Deployment，然后创建多个Service，用来观察路由的效果。实际测试请替换成您自己的服务。

```
kubectl run nginx --image=registry.cn-hangzhou.aliyuncs.com/acs/netdia:latest

kubectl expose deploy nginx --name=http-svc --port=80 --target-port=80
kubectl expose deploy nginx --name=http-svc1 --port=80 --target-port=80
kubectl expose deploy nginx --name=http-svc2 --port=80 --target-port=80
kubectl expose deploy nginx --name=http-svc3 --port=80 --target-port=80
```

## 简单的路由服务

通过以下命令创建一个简单的Ingress，所有对/svc路径的访问都会被路由到名为HTTP-SVC的服务。`nginx.ingress.kubernetes.io/rewrite-target: /`会将/svc路径重新定向到后端服务能够识别的/路径上面。

1.  部署以下模板，创建Ingress。

    ```
    cat <<EOF | kubectl create -f -
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: simple
      annotations:
        nginx.ingress.kubernetes.io/rewrite-target: /$2
    spec:
      rules:
      - http:
          paths:
          - path: /svc(/|$)(.*)
            backend:
              serviceName: http-svc
              servicePort: 80
    EOF
    ```

    执行以下命令，查看Ingress命令。

    ```
    kubectl get ing
    ```

    ```
    NAME            HOSTS         ADDRESS          PORTS     AGE
    simple          *             101.37.XX.XX     80        11s
    ```

2.  访问`http://101.37.XX.XX/svc`即可访问到Nginx服务。


## 基于域名的简单扇出路由

如果您有多个域名对外提供不同的服务，您可以生成以下的配置达到一个简单的基于域名的扇出效果。

```
cat <<EOF | kubectl create -f - 
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: simple-fanout
spec:
  rules:
  - host: foo.bar.com
    http:
      paths:
      - path: /foo
        backend:
          serviceName: http-svc1
          servicePort: 80
      - path: /bar
        backend:
          serviceName: http-svc2
          servicePort: 80
  - host: foo.example.com
    http:
      paths:
      - path: /film
        backend:
          serviceName: http-svc3
          servicePort: 80    
EOF
```

```
kubectl get ing
```

```
NAME            HOSTS         ADDRESS          PORTS     AGE
simple-fanout   *             101.37.XX.XX     80        11s
```

这时您可以通过`http://foo.bar.com/foo`访问到`http-svc1`服务；通过`http://foo.bar.com/bar`访问到`http-svc2`服务；通过`http://foo.example.com/film`访问到`http-svc3`服务。

**说明：**

-   如果是生产环境，您需要将您的这个域名指向上面返回的ADDRESS`101.37.XX.XX`。
-   如果是测试环境，您可以修改`hosts`文件添加一条域名映射规则。

    ```
    101.37.XX.XX foo.bar.com
    101.37.XX.XX foo.example.com
    ```


## 简单路由默认域名

如果您没有域名地址也没有关系，容器服务为Ingress服务绑定了一个默认域名，您可以通过这个域名来访问服务。域名的格式如下：`*.[cluster-id].[region-id].alicontainer.com`。您可以直接在控制台集群基本信息页获取到该地址。

您可以通过下面的配置借助该默认域名暴露两个服务。

```
cat <<EOF | kubectl create -f - 
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: shared-dns
spec:
  rules:
  - host: foo.[cluster-id].[region-id].alicontainer.com  ##替换为您集群默认的服务访问域名。
     http:
      paths:
      - path: /
        backend:
          serviceName: http-svc1
          servicePort: 80
  - host: bar.[cluster-id].[region-id].alicontainer.com  ##替换为您集群默认的服务访问域名。
     http:
      paths:
      - path: /
        backend:
          serviceName: http-svc2
          servicePort: 80    
EOF
```

```
kubectl get ing
```

```
NAME            HOSTS         ADDRESS          PORTS     AGE
shared-dns   foo.[cluster-id].[region-id].alicontainer.com,bar.[cluster-id].[region-id].alicontainer.com             47.95.160.171   80        40m
```

这时您可以通过`http://foo.[cluster-id].[region-id].alicontainer.com/`访问到`http-svc1`服务；通过`http://bar.[cluster-id].[region-id].alicontainer.com`访问到`http-svc2`服务。

## 配置安全的路由服务

支持多证书管理，为您的服务提供安全防护。

1.  准备您的服务证书。

    如果没有证书，可以通过下面的方法生成测试证书。

    **说明：** 域名与您的Ingress配置要一致。

    1.  执行以下命令，生成一个证书文件tls.crt、一个私钥文件tls.key。

        ```
        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
        ```

    2.  执行以下命令，创建密钥。

        通过该证书和私钥创建一个名为foo.bar的Kubernetes Secret。创建Ingress时需要引用这个Secret。

        ```
        kubectl create secret tls foo.bar --key tls.key --cert tls.crt
        ```

2.  创建一个安全的Ingress服务。

    ```
    cat <<EOF | kubectl create -f - 
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: tls-fanout
    spec:
      tls:
      - hosts:
        - foo.bar.com
        secretName: foo.bar
      rules:
      - host: foo.bar.com
        http:
          paths:
          - path: /foo
            backend:
              serviceName: http-svc1
              servicePort: 80
          - path: /bar
            backend:
              serviceName: http-svc2
              servicePort: 80
    EOF
    ```

    ```
    kubectl get ing
    ```

    ```
    NAME            HOSTS         ADDRESS          PORTS     AGE
    tls-fanout      *             101.37.XX.XX     80        11s
    ```

3.  配置`hosts`文件或者设置域名来访问该TLS服务。请参见[基于域名的简单扇出路由](#section_2en_rfc_98m)。

    您可以通过`http://foo.bar.com/foo`访问到`http-svc1`服务；通过`http://foo.bar.com/bar`访问到`http-svc2`服务。


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


## 通过Kubernetes Dashboard部署Ingress

1.  将下面的YAML Code保存到`nginx-ingress.yaml`文件中。

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: simple
    spec:
      rules:
      - http:
          paths:
          - path: /svc
            backend:
              serviceName: http-svc
              servicePort: 80
    ```

2.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

3.  在控制台左侧导航栏中，单击**集群**。

4.  在集群列表页面中，单击目标集群右侧的**操作**列的**更多**，选择**Dashboard**，进入Kubernetes Dashboard页面。

    ![Dashboard入口](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5285659951/p133862.png)

5.  单击页面右上角的**创建**，开始创建应用。

6.  单击**使用文件创建**。选择刚才保存的`nginx-ingress.yaml`文件。

7.  单击**上传**。

    这样就创建了一个Ingress的七层代理路由到`http-svc`服务上。

8.  单击左侧导航栏的**服务发现与负载均衡** \> **路由**，可以看到您刚刚创建的Ingress资源及其访问地址。

9.  打开浏览器输入该地址即可访问前面创建的`http-svc`服务。


## 配置域名支持正则化

在Kubernetes集群中，Ingress资源不支持对域名配置正则表达式，但是可以通过`nginx.ingress.kubernetes.io/server-alias`注解来实现。

1.  创建测试Ingress，以正则表达式`~^www\.\d+\.example\.com`为例。

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

2.  查看Ingress-nginx的配置，可以发现生效的配置（Server\_Name字段）。

    ```
    kubectl get pods -n kube-system | grep ingress
    ```

    预期输出：

    ```
    nginx-ingress-controller-77cd987c4c-cp87g         1/1     Running   0          1h
    nginx-ingress-controller-77cd987c4c-xvpt5         1/1     Running   0          1h
    ```

    ```
    kubectl exec -n kube-system nginx-ingress-controller-77cd987c4c-cp87g cat /etc/nginx/nginx.conf | grep -C3 "regex.ingress.test.com"
    ```

    预期输出：

    ```
    }
      ## end server _
    
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
    
      # backend for when default-backend-service is not configured or it does not have endpoints
      server {
    ```

3.  查看Ingress对应的IP。

    ```
    kubectl get ing
    ```

    预期输出：

    ```
    NAME            HOSTS         ADDRESS          PORTS     AGE
    ingress-regex   foo.bar.com   101.37.XX.XX     80        11s
    ```

4.  通过Curl命令行进行服务访问测试，如下IP变量替换为真实获取的IP地址。

    ```
    curl -H "Host: foo.bar.com" $IP/foo
    ```

    预期输出：

    ```
    /foo
    ```

    ```
    curl -H "Host: www.123.example.com" $IP/foo
    ```

    预期输出：

    ```
    /foo
    ```

    ```
    curl -H "Host: www.321.example.com" $IP/foo
    ```

    预期输出：

    ```
    /foo
    ```


