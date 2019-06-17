# 使用Helm部署微服务应用 {#concept_xfh_dnr_n2b .concept}

本文介绍如何将一个较复杂的应用部署到阿里云Kubernetes容器服务上，下面将从基础设施和应用部署的不同组合方式，来部署一个复杂的SpringCloud应用。

## 部署方式 {#section_dgh_2nr_n2b .section}

1.  基础设施（Eureka，ConfigServer）和应用一起部署。
2.  在容器服务上搭建好基础设施后，再部署应用。

## 示例应用PiggyMetrics {#section_egh_2nr_n2b .section}

[PiggyMetrics](https://github.com/sqshq/PiggyMetrics?spm=a2c4e.11153940.blogcont610430.14.40667737Uedu2h)是github上的一个SpringCloud应用项目，Star数目3400多。这个项目主体采用Docker Compose部署，包含了完整的源代码以及构建好的容器镜像，是非常不错的SpringCloud容器化示例。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15607519767362_zh-CN.png)

这个项目包含了3个业务微服务，分别是统计服务（Statistics Service）、账户服务（Account Service）和通知服务（Notification Service）。每个服务分别对应一个独立的MongoDB。微服务架构图示（采用作者原图）如下：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15607519767363_zh-CN.png)

SpringCloud基础组件为负责服务注册和registry服务（Eureka服务注册），config服务（配置管理），gateway（API网关，同时也是JavaScript Web界面），monitor服务（Hystrix Dashboard/Turbine）等。

本文所用到的部署描述文件地址在github上，若您感兴趣，请参考链接： [https://github.com/binblee/PiggyMetrics/tree/master/charts](https://github.com/binblee/PiggyMetrics/tree/master/charts?spm=a2c4e.11153940.blogcont610430.15.40667737Uedu2h)。

## 场景一 用helm一键部署所有服务 {#section_hgh_2nr_n2b .section}

PiggyMetrics的部署采用docker-compose YAML部署到单机，如果要部署到Kubernetes环境中 ，需要转换成为Kubernetes deployment YAML。[kompose](https://github.com/kubernetes/kompose)工具可以一键将compose文件转换为Kubernetes部署文件。

**说明：** PiggyMetrics中的[docker compose](https://github.com/sqshq/PiggyMetrics/blob/master/docker-compose.yml?spm=a2c4e.11153940.blogcont610430.17.40667737Uedu2h&file=docker-compose.yml)模版为2.1版，kompose不支持该版本，所以需要把compose文件改为版本2。

此外，还要去除kompose不支持的语法：

```
depends_on:
      config:
        condition: service_healthy  # 不支持 condition
```

增加Kubernetes server type annotation：

```
labels: 
      kompose.service.type: loadbalancer
```

读者可以参考已经更改好的compose文件 [https://github.com/binblee/PiggyMetrics/blob/master/charts/docker-compose.yml](https://github.com/binblee/PiggyMetrics/blob/master/charts/docker-compose.yml?spm=a2c4e.11153940.blogcont610430.18.40667737Uedu2h&file=docker-compose.yml)。

在执行kompose之前还需要设定PiggyMetrics部署所需的环境变量。

```
export NOTIFICATION_SERVICE_PASSWORD=passw0rd
export CONFIG_SERVICE_PASSWORD=passw0rd
export STATISTICS_SERVICE_PASSWORD=passw0rd
export ACCOUNT_SERVICE_PASSWORD=passw0rd
export MONGODB_PASSWORD=passw0rd
kompose convert -f docker-compose.yml -o piggymetrics -c
```

kompose的选项-c可以生成[helm chart](https://docs.helm.sh/chart_template_guide?spm=a2c4e.11153940.blogcont610430.19.40667737Uedu2h)格式目录结果，使用[helm](https://helm.sh/?spm=a2c4e.11153940.blogcont610430.20.40667737Uedu2h)命令行部署所有的服务。

```
charts $ helm install -n piggymetrics piggymetrics/
			
```

部署后可以看到输出成功信息。

您可以在自己的Minikube上尝试，或者部署到阿里云容器服务Kubernetes版：[https://www.aliyun.com/product/kubernetes](https://www.aliyun.com/product/kubernetes?spm=a2c4e.11153940.blogcont610430.21.40667737Uedu2h)。部署完成后进入服务列表页面，可以看到所有服务以及对应LoadBalancer类型Service对外暴露的访问地址及端口号。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15607519777369_zh-CN.png)

单击registry service可以进入到PiggyMetrics的界面。

PiggyMetrics是个人理财服务，用户输入收入和支出后可以展现漂亮的报表。

访问registry service，可以看到所有注册到Eureka Server上的服务。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15607519777371_zh-CN.png)

将PiggyMetrics删除，为下个实验做准备：

```
charts $ helm delete --purge piggymetrics
release "piggymetrics" deleted
```

## 场景二 部署应用到已有SpringCloud基础组件的环境中 {#section_ngh_2nr_n2b .section}

上面您看到的是如何将全部基础组件（Eureka，Zuul，ConfigServer，Hystrix Dashboard）和业务应用（gateway，notification，statistics）都统一用一个helm chart部署成功。在实际工作中，更常见的情况是在集群中已经有了Eureka等基础组件，您只需部署和升级维护业务应用。

在阿里云容器服务Kubernetes版中，**市场** \> **应用目录**中包含了SpringCloud基本组件。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15607519787390_zh-CN.png)

我们可以先从应用目录部署好Eureka服务。单击**ack-springcloud-eureka**组件，进入如下界面：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15607519787391_zh-CN.png)

您也可以单击**参数**，进入参数页面，查看或更改配置：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15607519787392_zh-CN.png)

在这里选择不改变任何参数，单击右侧**创建**，直接部署。

部署成功后，在阿里云容器服务控制台中，单击左侧导航栏中**路由与负载均衡** \> **服务**，选择集群和命名空间，进入服务列表页面。可以看到，EurekaServer有两个示例，对外暴露的服务地址为`ack-springcloud-eureka-default-ack-springcloud-eureka-svc`。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15607519787393_zh-CN.png)

在PiggyMetrics中，所有容器启动时自动访问的的Eureka服务名为`registry`。一般情况下，可以在镜像中把Eureka服务名作为参数传递，但是在这个实验中我们不准备改变任何代码和镜像，所以可以采取另外一个措施，那就是为Eureka多暴露一个叫**registry**的服务。

利用容器服务部署如下YAML文件。

**说明：** 如果读者在应用目录部署过程中更改了发布名称，下面的内容也要做相应调整。

```
apiVersion: v1
kind: Service
metadata:
  name: registry
spec:
  type: LoadBalancer
  ports:
    - port: 8761
      targetPort: 8761
  selector:
    app: ack-springcloud-eureka-default-ack-springcloud-eureka
    release: ack-springcloud-eureka-default
```

您可以利用kubectl命令行创建该服务：

```
$ kubectl apply -f  registry-svc.yml
```

您也可以通过控制台界面完成：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15607519797394_zh-CN.png)

部署完成后，单击左侧导航栏中**路由与负载均衡** \> **服务**，再次进入服务（Service）页面，可以看到reigstry创建成功：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15607519797395_zh-CN.png)

好了，下面把PiggyMetrics的helm chart目录拷贝到一个新的目录piggymetrics-no-eureka，删除以下两个文件。

```
templates/registry-deployment.yaml
templates/registry-service.yaml
```

这两个文件是分别部署Eureka deployment/svc的YAML文件，由于我们在前面已经用应用目录部署成功了一个新的registry服务作为基础SpringCloud组件，这里就不要再重复部署了。

执行helm命令再次部署PiggyMetrics。

```
$ helm install -n piggymetrics piggymetrics-no-eureka/
```

所有服务启动成功后，访问registry服务，可以看到所有PiggyMetrics服务均已正确地注册到了EurekaServer中。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15607519797397_zh-CN.png)

PiggyMetrics应用已经部署到了包含EurekaServer的环境上。访问`GATEWAY`即可看到熟悉的登录界面。

