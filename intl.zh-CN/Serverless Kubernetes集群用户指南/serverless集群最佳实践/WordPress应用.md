# WordPress应用

[打开Cloud Shell](https://shell.aliyun.com/?action=git_open&git_repo=https://code.aliyun.com/qinglin.dql/eci-wordpress.git&tutorial=tutorial-zh.md)

**说明：** 以下步骤均已在Cloud Shell中集成，可以通过打开上面的链接来快速体验通过Cloud Shell操作ECI。

## 创建Serverless Kubernetes集群

请参见[创建Serverless Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。

## 安装WordPress

**说明：** 请确保上一步中创建的 Serverless Kubernetes 集群，已完成初始化（一般需要3~5分钟），再开始以下的操作。

使用Cloud Shell来管理上一步中创建中的Serverless Kubernetes集群。

```
source use-k8s-cluster ${集群ID}
```

执行WordPress安装yaml文件。

```
kubectl apply -f wordpress-all-in-one-pod.yaml
```

观察安装进度，直到STATUS为Running。

```
kubectl get pods
```

查询EIP地址。

```
kubectl get -o json pod wordpress |grep "k8s.aliyun.com/allocated-eipAddress"
```

由于安全组默认没有开放 80 端口的访问，需要给安全组添加 80 端口的 ACL。

首先获取Serverless Kubernetes集群自动创建的安全组，找到最近创建的以`alicloud-cs-auto-created`为命名前缀的安全组ID。

```
aliyun ecs DescribeSecurityGroups|grep -B 1 'alicloud-cs-auto-created'|head -1
```

对安全组进行授权操作。

```
aliyun ecs AuthorizeSecurityGroup --RegionId cn-chengdu --SecurityGroupId ${安全组ID} --IpProtocol tcp --PortRange 80/80 --SourceCidrIp 0.0.0.0/0 --Priority 100
```

## 使用WordPress

在浏览器起输入上一步获取到的EIP地址，即可开始使用WordPress。

