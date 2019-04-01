# Use Ingress to provide Layer-7 service access {#concept_f2x_bfn_j2b .concept}

In the Alibaba Cloud Serverless Kubernetes cluster, Server Load Balancer provides Layer-4 service access. You can also use Layer-7 service access provided by Ingress. This document describes how to provide Layer-7 domain name service access in the Serverless Kubernetes cluster.

## Prerequisites {#section_e5m_bgn_j2b .section}

-   You have created a Serverless cluster. VPC cluster must be configured with a NAT gateway to access the external network and download the container image. 
-   You have connected to the cluster by using kubectl, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Serverless Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

## Instructions {#section_and_jfn_j2b .section}

1.  If Server Load Balancer is not specified, system automatically generates a public network Server Load Balancer instance.
2.  The default front-end listening ports for SLB instances are 80 \(HTTP Protocol\) and 443 \(HTTPS protocol \).
3.  By default, the HTTPS certificate of the SLB instance is initialized for the first created Ingress-configured TLS certificate. Otherwise, the system default certificate is initialized. You can modify it in the SLB console as needed.
4.  When you specify to use an existing SLB instance, SLB instance specification must be of performance guarantee type \(supports ENI\). Also, make sure that ports 80 and 443 are not currently used by other services. 

## Annotations {#section_htp_kfn_j2b .section}

|Note|Description|
|----|-----------|
|service.beta.kubernetes.io/alicloud-loadbalancer-id|Specify an existing SLB ID.|

## Use the default generated SLB instance. {#section_j5s_3fn_j2b .section}

If an SLB instance is not specified, the system automatically generates a performance guaranteed public network SLB instance when the first Ingress is created.

**1. Deploy test services**

Deploy a coffee service and tea service first, the layout template is as follows:

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
$ kubectl apply -f cafe-service.yaml                   #Enter the template above in the cafe-service.yaml file
deployment "coffee" created
service "coffee-svc" created
deployment "tea" created
service "tea-svc" created

# After deployment is complete

$ kubectl get svc,deploy
NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
svc/coffee-svc   ClusterIP   <none>       <none>        80/TCP    1m
svc/tea-svc      ClusterIP   <none>       <none>        80/TCP    1m

NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/coffee   2         2         2            2           1m
deploy/tea      1         1         1            1           1m
```

**2. Configure Ingress**

Configure the domain name and path exposed by coffee service and tea service by using Ingress:

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: cafe-ingress
spec:
  rules:
  # Configure Layer-7 domain name
  - host: foo.bar.com
    http:
      paths:
      # Configure context path
      - path: /tea
        backend:
          serviceName: tea-svc
          servicePort: 80
      # Configure context path
      - path: /coffee
        backend:
          serviceName: coffee-svc
          servicePort: 80
```

```
$ kubectl apply -f cafe-ingress.yaml
ingress "cafe-ingress" created
  
# The address is the IP address of the automatically generated SLB instance.

$ kubectl get ing
NAME           HOSTS         ADDRESS          PORTS     AGE
cafe-ingress   foo.bar.com   139.***.**.***   80        1m
```

**3. Test service access**

**Note:** Currently, the domain name of the SLB instance IP must be resolved manually.

In this example, a DNS domain name resolution rule is added to hosts for testing service access. We recommend that you enter the domain name in your work environment.

```
139.224.76.211    foo.bar.com
```

Test access to the coffee service by using browser.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16502/155412486610319_en-US.png)

Test access to the coffee service by using command line.

```
curl -H "Host: foo.bar.com" http://139.***.**.***/coffee
```

Test access to the tea service by using browser.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16502/155412486710320_en-US.png)

Test access to the tea service by using command line.

```
curl -H "Host: foo.bar.com" http://139.***.**.***/tea
```

## Use the specified SLB instance {#section_n5s_3fn_j2b .section}

You can specify to use of an existing SLB instance by using the `service.beta.kubernetes.io/alicloud-loadbalancer-id` annotation, but the SLB instance specification must be of performance guarantee type \(supports ENI\).

**Note:** System automatically initializes ports 80 and 443 of the SLB instance, make sure that ports are not currently used by other services. 

**1. Deploy test services**

Deploy a Tomcat test application first, the layout template is as follows:

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
$ kubectl apply -f tomcat-service.yml                 #Enter the template above in tomcat-service.yaml
deployment "tomcat" created
service "tomcat" created

# After deployment is complete

$ kubectl get svc,deploy tomcat
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
svc/tomcat   ClusterIP   <none>       <none>        8080/TCP   1m

NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/tomcat   1         1         1            1           1m
```

**2. Apply for SLB instance**

You must apply for a **performance guarantee type** SLB instance \(such as slb.s2.small\) under the cluster and region. According to the specific needs, private or public network can be used.  In this example, apply for a public network SLB instance and record the ID of the SLB instance.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16502/155412486710321_en-US.png)

**3. Configure the TLS certificate**

You must configure the TLS certificate for HTTPS access.

**Note:** System automatically initializes the SLB HTTPS default certificate according to the first created Ingress TLS certificate. If you want to modify the HTTPS default certificate, you can modify it in the SLB console. If you want to configure multiple certificates, you can manually add them in the SLB console HTTPS listener domain name extension.

```
# Generate test TLS certificate
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=bar.foo.com/O=bar.foo.com"# Create a TLS certificate secret
$ kubectl create secret tls cert-example --key tls.key --cert tls.crt
secret "cert-example" created

# View new TLS certificate

$ kubectl get secret cert-example
NAME           TYPE                DATA      AGE
cert-example   kubernetes.io/tls   2         12s
```

**4. Configure Ingress**

Configure the domain name and path exposed by Tomcat service by using Ingress. The layout template is as follows: 

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: tomcat-ingress
  annotations:
    # Configure to use the specified SLB instance (SLB ID)
    service.beta.kubernetes.io/alicloud-loadbalancer-id: lb-xxxxxxxxxx            ##Replace with your SLB ID
spec:
  tls:
  - hosts:
    - bar.foo.com
    # Configure the TLS certificate
    secretName: cert-example
  rules:
  # Configure Layer-7 domain name
  - host: bar.foo.com
    http:
      paths:
      # Configure context path
      - path: /
        backend:
          serviceName: tomcat
          servicePort: 8080
```

```
$ kubectl apply -f tomcat-ingress.yml                       #Enter the template above in tomcat-ingress.yaml
ingress "tomcat-ingress" created

# After deployment is complete, ADDRESS is the specified SLB IP address

$ kubectl get ing tomcat-ingress
NAME             HOSTS         ADDRESS        PORTS     AGE
tomcat-ingress   bar.foo.com   47.101.20.67   80, 443   1m
```

```
$ kubectl apply -f tomcat-ingress.yml                       #In the tomcat-ingress.yaml file, enter the template above in tomcat-ingress.yaml.
ingress "tomcat-ingress" created

# After the deployment,the address is the specified SLB instance IP address.

$ kubectl get ing tomcat-ingress
NAME             HOSTS         ADDRESS        PORTS     AGE
tomcat-ingress   bar.foo.com   47.***.**.**   80, 443   1m
```

**5. Test service access**

**Note:** Currently, the domain name of the SLB instance IP must be resolved manually.

In this example, a DNS domain name resolution rule is added to hosts for testing service access. We recommend that you enter the domain name in your work environment.

```
47.101.20.67   bar.foo.com
```

Test access to Tomcat service by using browser:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16502/155412486710322_en-US.png)

Test access to Tomcat service by using command line:

`curl -k -H "Host: bar.foo.com" https://47.***.**.**`

