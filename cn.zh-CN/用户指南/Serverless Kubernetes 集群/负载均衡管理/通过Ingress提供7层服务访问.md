# 通过Ingress提供7层服务访问 {#concept_f2x_bfn_j2b .concept}

在阿里云Serverless Kubernetes集群中，我们可以通过LoadBalancer Service对外提供四层服务访问，同样您也可以通过Ingress来对外提供七层服务访问，下面介绍如何在Serverless Kubernetes集群中提供七层域名服务访问。

## 前提条件 {#section_e5m_bgn_j2b .section}

-   您已创建一个serverless集群，集群的VPC需要配置NAT网关，从而访问外网，下载容器镜像。
-   您已通过kubectl连接到集群，参见[通过 kubectl 连接 Kubernetes 集群](cn.zh-CN/用户指南/Serverless Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

## 使用说明 {#section_and_jfn_j2b .section}

1.  不指定SLB实例情况下系统会自动帮您生成一个公网SLB实例。
2.  SLB实例默认前端监听端口为80（HTTP协议）和443（HTTPS协议）。
3.  SLB实例HTTPS证书默认会初始化为第一个创建的Ingress配置的TLS证书，否则会初始化为系统默认证书；您可根据需要自行在SLB控制台上进行修改。
4.  当您指定使用已存在的SLB实例时，要求该SLB实例规格必须是性能保障型（支持ENI）；同时确保80和443端口当前没有其他服务使用。

## 注释说明 {#section_htp_kfn_j2b .section}

|注释|说明|
|--|--|
|service.beta.kubernetes.io/alicloud-loadbalancer-id|指定已存在的SLB ID|

## 使用默认生成的SLB实例 {#section_j5s_3fn_j2b .section}

当您不指定SLB实例时，系统会在第一个Ingress创建时自动生成一个性能保障型的公网SLB实例。

**1、 部署测试服务**

首先部署一个coffee service和tea service，编排模板如下：

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: coffee
spec:
  replicas: 2
  selector:
    matchLabels:
      app: coffee
  template:
    metadata:
      labels:
        app: coffee
    spec:
      containers:
      - name: coffee
        image: registry.cn-hangzhou.aliyuncs.com/acs-sample/nginxdemos:latest
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: coffee-svc
spec:
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
  selector:
    app: coffee
  clusterIP: None
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: tea
spec:
  replicas: 1
  selector:
    matchLabels:
      app: tea 
  template:
    metadata:
      labels:
        app: tea 
    spec:
      containers:
      - name: tea 
        image: registry.cn-hangzhou.aliyuncs.com/acs-sample/nginxdemos:latest
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: tea-svc
  labels:
spec:
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
  selector:
    app: tea
  clusterIP: None
```

```
$ kubectl apply -f cafe-service.yaml                   #在cafe-service.yaml文件中输入上面的模板
deployment "coffee" created
service "coffee-svc" created
deployment "tea" created
service "tea-svc" created

# 部署完成后

$ kubectl get svc,deploy
NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
svc/coffee-svc   ClusterIP   <none>       <none>        80/TCP    1m
svc/tea-svc      ClusterIP   <none>       <none>        80/TCP    1m

NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/coffee   2         2         2            2           1m
deploy/tea      1         1         1            1           1m
```

**2、配置 Ingress**

通过Ingress配置coffee service和tea service对外暴露的域名和Path路径：

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: cafe-ingress
spec:
  rules:
  # 配置七层域名
  - host: foo.bar.com
    http:
      paths:
      # 配置Context Path
      - path: /tea
        backend:
          serviceName: tea-svc
          servicePort: 80
      # 配置Context Path
      - path: /coffee
        backend:
          serviceName: coffee-svc
          servicePort: 80
```

```
$ kubectl apply -f cafe-ingress.yaml
ingress "cafe-ingress" created
  
# 部署完成后，ADDRESS为自动生成的SLB实例IP

$ kubectl get ing
NAME           HOSTS         ADDRESS          PORTS     AGE
cafe-ingress   foo.bar.com   139.224.76.211   80        1m
```

**3、测试服务访问**

**说明：** 目前我们需要自行将域名解析到SLB实例IP上

本例中在hosts中添加一条DNS域名解析规则，用于测试服务访问。建议您在工作环境中对域名进行备案。

```
139.224.76.211    foo.bar.com
```

通过浏览器测试访问coffee服务。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16502/154821594110319_zh-CN.png)

通过命令行方式测试访问coffee服务。

```
curl -H "Host: foo.bar.com" http://139.224.76.211/coffee
```

通过浏览器测试访问tea服务。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16502/154821594110320_zh-CN.png)

通过命令行方式测试访问tea服务。

```
curl -H "Host: foo.bar.com" http://139.224.76.211/tea
```

## 使用指定的SLB实例 {#section_n5s_3fn_j2b .section}

我们可以通过注释`service.beta.kubernetes.io/alicloud-loadbalancer-id`来指定使用已存在的SLB实例，但要求该SLB实例必须为性能保障型规格（支持ENI）。

**说明：** 系统会自动初始化SLB实例的80和443端口，请确保当前没有其他服务使用。

**1、 部署测试服务**

首先部署一个tomcat测试应用，编排模板如下：

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: tomcat
spec:
  replicas: 1
  selector:
    matchLabels:
      run: tomcat
  template:
    metadata:
      labels:
        run: tomcat
    spec:
      containers:
      - image: tomcat:7.0
        imagePullPolicy: Always
        name: tomcat
        ports:
        - containerPort: 8080
          protocol: TCP
      restartPolicy: Always
---
apiVersion: v1
kind: Service
metadata:
  name: tomcat
spec:
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080
  selector:
    run: tomcat
  clusterIP: None
```

```
$ kubectl apply -f tomcat-service.yml                 #在tomcat-service.yaml中输入上面的模板
deployment "tomcat" created
service "tomcat" created

# 部署完成后

$ kubectl get svc,deploy tomcat
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
svc/tomcat   ClusterIP   <none>       <none>        8080/TCP   1m

NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/tomcat   1         1         1            1           1m
```

**2、申请SLB实例**

您需要在集群同Region下自行申请一个**性能保障型**SLB实例（如slb.s2.small），可以是私网也可以是公网（依据具体需求）。本例中申请一个公网SLB实例，记录SLB实例的ID。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16502/154821594110321_zh-CN.png)

**3、配置TLS证书**

您需要配置TLS证书实现HTTPS访问。

**说明：** 系统自动依据第一个创建的Ingress的TLS证书来初始化SLB的HTTPS默认证书，若需要修改HTTPS默认证书，可在SLB控制台自行修改；若需配置多个证书，可在SLB控制台HTTPS监听扩展域名下自行添加。

```
# 生成测试TLS证书
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=bar.foo.com/O=bar.foo.com"# 创建TLS证书Secret
$ kubectl create secret tls cert-example --key tls.key --cert tls.crt
secret "cert-example" created

# 查看新建TLS证书

$ kubectl get secret cert-example
NAME           TYPE                DATA      AGE
cert-example   kubernetes.io/tls   2         12s
```

**4、配置 Ingress**

通过Ingress配置tomcat service对外暴露的域名和Path路径，编排模板如下：

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: tomcat-ingress
  annotations:
    # 配置使用指定的SLB实例（SLB ID）
    service.beta.kubernetes.io/alicloud-loadbalancer-id: lb-xxxxxxxxxx            ##替换为你的SLB ID
spec:
  tls:
  - hosts:
    - bar.foo.com
    # 配置TLS证书
    secretName: cert-example
  rules:
  # 配置七层域名
  - host: bar.foo.com
    http:
      paths:
      # 配置Context Path
      - path: /
        backend:
          serviceName: tomcat
          servicePort: 8080
```

```
$ kubectl apply -f tomcat-ingress.yml                       #在tomcat-ingress.yaml中输入上面的模板
ingress "tomcat-ingress" created

# 部署完成后，ADDRESS为指定的SLB IP地址

$ kubectl get ing tomcat-ingress
NAME             HOSTS         ADDRESS        PORTS     AGE
tomcat-ingress   bar.foo.com   47.101.20.67   80, 443   1m
```

**5、测试服务访问**

**说明：** 目前我们需要自行将域名解析到SLB实例IP上。

本例中在hosts中添加一条DNS域名解析规则，用于测试服务访问。建议您在工作环境中对域名进行备案。

```
47.101.20.67   bar.foo.com
```

通过浏览器测试访问tomcat服务：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16502/154821594110322_zh-CN.png)

通过命令行方式测试访问tomcat服务：

`curl -k -H "Host: bar.foo.com" https://47.101.20.67`

