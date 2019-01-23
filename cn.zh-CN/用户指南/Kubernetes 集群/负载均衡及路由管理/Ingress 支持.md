# Ingress 支持 {#concept_zny_hrs_vdb .concept}

在 Kubernetes 集群中，Ingress是授权入站连接到达集群服务的规则集合，为您提供七层负载均衡能力。您可以给 Ingress 配置提供外部可访问的 URL、负载均衡、SSL、基于名称的虚拟主机等。

## 前置条件 {#section_bnd_jrs_vdb .section}

为了测试复杂路由服务，本例中创建一个 nginx 的示例应用，您需要事先创建 nginx 的 deployment，然后创建多个 Service，用来观察路由的效果。实际测试请替换成自己的服务。

```
root@master # kubectl run nginx --image=registry.cn-hangzhou.aliyuncs.com/acs/netdia:latest

root@master # kubectl expose deploy nginx --name=http-svc --port=80 --target-port=80
root@master # kubectl expose deploy nginx --name=http-svc1 --port=80 --target-port=80
root@master # kubectl expose deploy nginx --name=http-svc2 --port=80 --target-port=80
root@master # kubectl expose deploy nginx --name=http-svc3 --port=80 --target-port=80
```

## 简单的路由服务 {#section_dnd_jrs_vdb .section}

通过以下命令创建一个简单的 Ingress，所有对 /svc 路径的访问都会被路由到名为 http-svc 的服务。`nginx.ingress.kubernetes.io/rewrite-target: /`会将/svc路径重定向到后端服务能够识别的/路径上面。

```
root@master # cat <<EOF | kubectl create -f -
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: simple
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /svc
        backend:
          serviceName: http-svc
          servicePort: 80
EOF
root@master # kubectl get ing
NAME            HOSTS         ADDRESS          PORTS     AGE
simple          *             101.37.192.211   80        11s
```

现在访问 `http://101.37.192.211/svc` 即可访问到 Nginx 服务。

## 基于域名的简单扇出路由 {#section_lnd_jrs_vdb .section}

如果您有多个域名对外提供不同的服务，您可以生成如下的配置达到一个简单的基于域名的扇出效果。

```
root@master # cat <<EOF | kubectl create -f - 
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
root@master # kubectl get ing
NAME            HOSTS         ADDRESS          PORTS     AGE
simple-fanout   *             101.37.192.211   80        11s
```

这时您可以通过 `http://foo.bar.com/foo` 访问到 `http-svc1` 服务；通过 `http://foo.bar.com/bar` 访问到 `http-svc2` 服务；通过 `http://foo.example.com/film` 访问到 `http-svc3` 服务。

**说明：** 

-   如果是生产环境，您需要将您的这个域名指向上面返回的 ADDRESS `101.37.192.211`。
-   如果是测试环境测试，您可以修改 `hosts` 文件添加一条域名映射规则。

    ```
    101.37.192.211 foo.bar.com
    101.37.192.211 foo.example.com
    ```


## 简单路由默认域名 {#section_znd_jrs_vdb .section}

如果您没有域名地址也没有关系，容器服务为 Ingress 服务绑定了一个默认域名，您可以通过这个域名来访问服务。域名的格式如下：`*.[cluster-id].[region-id].alicontainer.com`。您可以直接在控制台集群基本信息页获取到该地址。

您可以通过下面的配置借助该默认域名暴露两个服务。

```
root@master # cat <<EOF | kubectl create -f - 
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: shared-dns
spec:
  rules:
  - host: foo.[cluster-id].[region-id].alicontainer.com  ##替换为您集群默认的服务访问域名
    http:
      paths:
      - path: /
        backend:
          serviceName: http-svc1
          servicePort: 80
  - host: bar.[cluster-id].[region-id].alicontainer.com  ##替换为您集群默认的服务访问域名
    http:
      paths:
      - path: /
        backend:
          serviceName: http-svc2
          servicePort: 80    
EOF
root@master # kubectl get ing
NAME            HOSTS         ADDRESS          PORTS     AGE
shared-dns   foo.[cluster-id].[region-id].alicontainer.com,bar.[cluster-id].[region-id].alicontainer.com             47.95.160.171   80        40m
```

这时您可以通过 `http://foo.[cluster-id].[region-id].alicontainer.com/` 访问到 `http-svc1` 服务；通过 `http://bar.[cluster-id].[region-id].alicontainer.com` 访问到 `http-svc2` 服务。

## 配置安全的路由服务 {#section_j4d_jrs_vdb .section}

支持多证书管理，为您的服务提供安全防护。

1.  准备您的服务证书。

    如果没有证书，可以通过下面的方法生成测试证书。

    **说明：** 域名与您的 Ingress 配置要一致。

    ```
    root@master # openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=foo.bar.com/O=foo.bar.com"
    ```

    上面命令会生成一个证书文件tls.crt、一个私钥文件tls.key。

    然后用该证书和私钥创建一个名为foo.bar的 Kubernetes Secret。创建 Ingress 时需要引用这个 Secret。

    ```
    root@master # kubectl create secret tls foo.bar --key tls.key --cert tls.crt
    ```

2.  创建一个安全的 Ingress 服务。

    ```
    root@master # cat <<EOF | kubectl create -f - 
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
    root@master # kubectl get ing
    NAME            HOSTS         ADDRESS          PORTS     AGE
    tls-fanout      *             101.37.192.211   80        11s
    ```

3.  按照 **基于域名的简单扇出路由** 中的注意事项，配置 `hosts` 文件或者设置域名来访问该 tls 服务。

    您可以通过 `http://foo.bar.com/foo` 访问到 `http-svc1` 服务；通过 `http://foo.bar.com/bar` 访问到 `http-svc2` 服务。

    您也可以通过 HTTP 的方式访问该 HTTPS 的服务。Ingress 默认对配置了 HTTPS 的 HTTP 访问重定向到 HTTPS 上面。所以访问 `http://foo.bar.com/foo` 会被自动重定向到 `https://foo.bar.com/foo`。


## 通过 Kubernetes Dashboard 部署 Ingress {#section_vvf_12y_wdb .section}

1.  将下面的 yml code 保存到 `nginx-ingress.yml` 文件中。

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

2.  登录[容器服务管理控制台](https://cs.console.aliyun.com)，在 Kubernetes 菜单下，在集群列表页面中，单击目标集群右侧的**控制台**，进入 Kubernetes Dashboard 页面。
3.  单击**创建**，开始创建应用。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16679/154821720010659_zh-CN.png)

4.  单击**使用文件创建**。选择刚才保存的 `nginx-ingress.yml` 文件。
5.  单击**上传**。

    这样就创建了一个 Ingress 的七层代理路由到 `http-svc` 服务上。

6.  在 Kubernetes Dashboard 上定位到 default 命名空间，选择**访问权**。

    可以看到您刚刚创建的 Ingress 资源及其访问地址 `http://118.178.174.161/svc`。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16679/154821720010660_zh-CN.png)

7.  打开浏览器输入该地址即可访问前面创建的 `http-svc` 服务。

