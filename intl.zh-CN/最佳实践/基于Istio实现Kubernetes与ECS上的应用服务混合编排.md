# 基于Istio实现Kubernetes与ECS上的应用服务混合编排 {#concept_qzh_bb5_cfb .concept}

Istio从0.2开始就提供了一个称之为Mesh Expansion\(中文大多称之为网格扩展\)的功能。它的主要功能是可以把一些非Kubernetes服务（这些服务往往是运行在其他一些虚拟机或物理裸机中）集成到运行在Kubernetes集群上的Istio服务网格中。

阿里云Kubernetes容器服务已经提供了Istio Mesh Expansion整合能力，本文通过一个官方示例来重点介绍如何使用Istio提供Kubernetes与阿里云ECS上的应用服务混合编排能力。

## 网格扩展Mesh Expansion {#section_gvz_cb5_cfb .section}

Mesh Expansion就是指部署在 Kubernetes 之中的 Istio 服务网格提供的一种将虚拟机或物理裸机集成进入到服务网格的方法。

Mesh Expansion对于用户从遗留系统往云上迁移过程中有着非常重要的作用，在微服务体系结构中，无法要求所有的工作负载都在Kubernetes中运行，用户的一些应用程序可能在Kubernetes中运维，而另外一些可能在虚拟机或物理裸机中运行。

通过一套Istio控制面板就可以管理跨Kubernetes与虚拟机或物理裸机的若干服务。这样既能保证原有业务的正常运转，又能实现Kubernetes与虚拟机上的应用服务混合编排的能力。

## 准备Kubernetes集群并安装Istio {#section_hvz_cb5_cfb .section}

阿里云容器服务Kubernetes 1.10.4目前已经上线，可以通过容器服务管理控制台非常方便地快速创建 Kubernetes 集群。具体过程可以参考[创建Kubernetes集群](../../../../intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。

**说明：** 确保通过kubectl 能够连接上Kubernetes 集群，参见[通过 kubectl 连接 Kubernetes 集群](../../../../intl.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

接着，就像前面系列文章中介绍的步骤，通过应用目录简便部署Istio。首先通过命令行或者控制台创建命名空间`istio-system`。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧的**市场** \> **应用目录**，在右侧选中`ack-istio`。
3.  在打开的页面中选择命名空间 `istio-system`，并单击**参数**，可以通过修改参数配置进行定制化安装。

**说明：** 说明文档提供了安装和卸载的一些重要信息，特别是常见的CRD（custom resource definition）版本问题。

## 安装示例到Kubernetes集群中 {#section_jvz_cb5_cfb .section}

首先通过如下命令行或者控制台创建命名空间 `bookinfo`，并部署我们修改之后的应用。在这个修改后的版本中去掉了`details`组件，并定义了`ingressgateway` 。

本示例中涉及到的文件可以从[示例](https://github.com/osswangxining/istio-meshexpansion?spm=a2c4e.11153940.blogcont622558.29.35307db13kaxoA)获取到。

```
kubectl create ns bookinfo
kubectl label namespace bookinfo istio-injection=enabled
kubectl apply -n bookinfo -f ./bookinfo/bookinfo-without-details.yaml
kubectl apply -n bookinfo -f ./bookinfo/bookinfo-gateway.yaml
```

基于官方示例修改的部署，其中`details`组件和数据库运行在Kubernetes之外的ECS上：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/153804282912682_zh-CN.png)

正常运行之后，通过`ingressgateway`暴露的地址访问`/productpage`页面，效果应该如下所示，`details`部分应该不能正常显示：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/153804282912683_zh-CN.png)

## 设置 Kubernetes {#section_lvz_cb5_cfb .section}

1.  首先，如果在安装Istio时没有为 Kube DNS、Pilot、Mixer 以及 Citadel 设置内部负载均衡器的话，则需要进行进行这步设置，命令如下：

    ```
    kubectl apply -f ./mesh-expansion.yaml
    ```

    4个服务创建如下：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/153804282912684_zh-CN.png)

2.  接着，生成 Istio 的配置cluster.env与 DNS 配置文件kubedns，用来在虚拟机上进行配置。其中cluster.env文件包含了将要拦截的集群 IP 范围，kubedns文件则是让虚拟机上的应用能够解析集群的服务名称，然后被 Sidecar 劫持和转发。

    执行命令如下：

    ```
    ./setupMeshEx.sh generateClusterEnvAndDnsmasq
    ```

    生成的cluster.env配置文件的示例：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/153804282912685_zh-CN.png)

    生成的kubedns文件的示例：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/153804282912686_zh-CN.png)


## 设置ECS {#section_nvz_cb5_cfb .section}

配置你自己的工作环境能与ECS实例的授权，生成SSH key并分发到ECS中。可以通过`ssh root@<ECS_HOST_IP>`命令确认是否可以成功连上ECS虚机。

生成公钥：

```
ssh-keygen -b 4096 -f ~/.ssh/id_rsa -N ""
```

**说明：** 为保证ECS能与Kubernetes网络上可通，可以将ECS与Kubernetes加入到同一个安全组。

阿里云容器服务针对ECS的设置步骤提供了较好的用户体验，通过运行以下脚本即可完成配置：

```
export SERVICE_NAMESPACE=default
./setupMeshEx.sh machineSetup root@<ECS_HOST_IP>
```

检查运行的进程：

```
ps aux |grep istio
```

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/153804282912687_zh-CN.png)

Istio 认证使用的 Node Agent 健康运行：

```
sudo systemctl status istio-auth-node-agent
```

## 在ECS上运行务 {#section_qvz_cb5_cfb .section}

由前面示例部署图知道，有2个服务需要运行在ECS上，一个是Details服务，另一个是数据库服务。

## 在ECS上运行Details服务 {#section_rvz_cb5_cfb .section}

通过以下命令模拟\(仅仅是用Docker模拟而已\)一个`Details`服务，运行在ECS上并暴露端口9080 。

```
docker pull istio/examples-bookinfo-details-v1:1.8.0
docker run -d -p 9080:9080 --name details-on-vm istio/examples-bookinfo-details-v1:1.8.0
```

配置 Sidecar 来拦截端口，这一配置存在于/var/lib/istio/envoy/sidecar.env，使用环境变量ISTIO\_INBOUND\_PORTS 。

示例 \(在运行服务的虚拟机上\)：

```
echo"ISTIO_INBOUND_PORTS=9080,8080" > /var/lib/istio/envoy/sidecar.env
systemctl restart istio
```

## 注册Details服务到Istio {#section_svz_cb5_cfb .section}

查找虚拟机的 IP 地址，用来加入服务网格：

```
hostname -I
```

手工配置一个没有选择器的服务和端点，用来承载没有对应 Kubernetes Pod 的服务。例如在一个有权限且能够使用 istioctl 命令的服务器上，注册`Details`服务：

```
istioctl -n bookinfo register details 192.168.3.202 http:9080
```

再次访问`/productpage`页面，效果应该如下所示，`details`部分应该可以正常显示

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/153804282912688_zh-CN.png)

## 将ratings服务切到数据库版本 {#section_tvz_cb5_cfb .section}

默认ratings服务不访问数据库，通过如下命令更新版本，使得ratings服务切换到访问数据库的版本：

```
kubectl apply -f ./bookinfo/bookinfo-ratings-v2-mysql-vm.yaml
kubectl apply -f ./bookinfo/virtual-service-ratings-mysql-vm.yaml
```

此时访问`/productpage`页面，效果应该如下所示，`ratings`部分不能正常显示，下一步就是在ECS上搭建数据库服务，并将之加入到Istio中：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/153804283012689_zh-CN.png)

## 在ECS上运行数据库服务 {#section_vvz_cb5_cfb .section}

在虚拟机上运行 MariaDB，将其作为 ratings 服务的后端；并配置MariaDB可以支持远程访问。

```
apt-get update && apt-getinstall -y mariadb-server
sed -i 's/127\.0\.0\.1/0\.0\.0\.0/g' /etc/mysql/mariadb.conf.d/50-server.cnf
sudo mysql
# 授予 root 权限
GRANT ALL PRIVILEGESON *.* TO'root'@'localhost'IDENTIFIEDBY'password'WITHGRANTOPTION;
quit;
sudo systemctl restart mysql
```

在虚拟机上把初始化 ratings 数据库。

```
curl -q https://raw.githubusercontent.com/istio/istio/master/samples/bookinfo/src/mysql/mysqldb-init.sql | mysql -u root -ppassword
```

为了更清晰的观察 Bookinfo 应用在输出方面的差异，可以用下面的命令来修改评级记录，从而生成不同的评级显示：

```
mysql -u root -ppassword test -e "select * from ratings;"
mysql -u root -ppassword test -e  "update ratings set rating=2;select * from ratings;"
```

## 注册数据库服务到Istio {#section_wvz_cb5_cfb .section}

配置 Sidecar 来拦截端口，这一配置存在于/var/lib/istio/envoy/sidecar.env，使用环境变量ISTIO\_INBOUND\_PORTS 。

示例 \(在运行服务的虚拟机上\):

```
echo"ISTIO_INBOUND_PORTS=3306,9080,8080" > /var/lib/istio/envoy/sidecar.env
systemctl restart istio
```

同样地，在一个有权限且能够使用 istioctl 命令的服务器上，注册数据库服务：

```
istioctl-nbookinforegistermysqldb 192.168.3.202 3306
```

经过这个步骤，Kubernetes Pod 和其他网格扩展包含的服务器就可以访问运行于这一服务器上的数据库服务了。

此时访问`/productpage`页面，效果应该如下所示，details 与 ratings 部分均能正常显示，而且这2个服务都是来自于ECS：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/153804283012690_zh-CN.png)

## 总结 {#section_zvz_cb5_cfb .section}

阿里云Kubernetes容器服务已经提供了Istio Mesh Expansion整合能力，本文通过一个官方示例来重点介绍如何使用Istio打通Kubernetes与阿里云ECS上的应用服务混合编排能力。

欢迎大家使用阿里云上的容器服务，快速搭建微服务的开放治理平台Istio，比较简单地集成到自己项目的微服务开发中。

