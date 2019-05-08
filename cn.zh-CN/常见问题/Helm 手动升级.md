# Helm 手动升级 {#concept_f4p_nl4_d2b .concept}

登录到Kubernetes集群master节点，参见[通过 kubectl 连接 Kubernetes 集群](../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

执行以下命令。

```
helm init --tiller-image registry.cn-hangzhou.aliyuncs.com/acs/tiller:v2.11.0 --upgrade
```

其中镜像地址可使用对应region的vpc域名，比如杭州region的机器就可以替换为registry-vpc.cn-hangzhou.aliyuncs.com/acs/tiller:v2.11.0。

然后等待tiller健康检查通过，就可以通过执行`helm version`查看版本升级情况。

**说明：** 这里只会升级Helm 服务端版本，客户端可以通过直接下载对应的client binary 使用。

Helm 2.11.0 client 下载地址: [https://github.com/helm/helm/releases/tag/v2.11.0](https://github.com/helm/helm/releases/tag/v2.11.0)。目前阿里云支持的最新版本为2.11.0。

Helm 客户端和服务端版本都升级完毕后，执行helm version命令，可看到如下信息。

```
$ helm version
Client: &version.Version{SemVer:"v2.11.0", GitCommit:"2e55dbe1fdb5fdb96b75ff144a339489417b146b", GitTreeState:"clean"}
Server: &version.Version{SemVer:"v2.11.0", GitCommit:"2e55dbe1fdb5fdb96b75ff144a339489417b146b", GitTreeState:"clean"}
```

