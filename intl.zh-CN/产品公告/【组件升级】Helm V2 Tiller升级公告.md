# 【组件升级】Helm V2 Tiller升级公告

近期阿里云容器服务Kubernetes版（ACK）新建集群中安装的Helm已全面升级至v3版本。由于Helm v2 Tiller服务端在社区一直存在已知的安全问题，攻击者可以通过tiller在集群内安装未经授权的应用，因此推荐您升级至Helm v3版本。

## 影响范围

首先执行命令kubectl get deploy -n kube-system tiller-deploy查看是否存在tiller deployment。如果存在，判断以下条件：

-   ACK集群访问密钥（kubeconfig）是否提供给外部客户使用。
-   阿里云容器服务控制台是否提供给外部客户登录访问。
-   ACK集群是否应用在多租户场景下，且有用户之间权限隔离的情况存在。

如果满足以上任何一点，建议您将安装的Helm升级到Helm v3系统。

## 未在上述范围或者暂时无法升级Helm v3

对于不在影响范围内或暂时无法升级Helm v3的用户可以手动升级Helm v2 tiller到最新版本，以获取更加强健的安全性保障。升级方式如下。

1.  执行以下命令。

    ```
    helm init --tiller-image registry.cn-hangzhou.aliyuncs.com/acs/tiller:v2.16.3 --upgrade
    ```

2.  待tiller健康检查通过后，执行命令helm version查看版本升级情况。

    这里只会升级 Helm服务端。客户端在各平台的最新版下载地址，请参见[下载地址](https://github.com/helm/helm/releases/tag/v2.16.3)。


**说明：** 未在上述范围内的用户，或者暂时无法升级Helm v3的用户，将Tiller升级到最新版即可，不需要再执行下面的操作，后续可以根据自己需求逐步地迁移至Helm v3。

## 前置检查

升级Helm v2之前，请进行前置检查。

1.  首先判断自己集群内是否含有tiller。执行命令kubectl get deploy -n kube-system tiller-deploy查看是否存在此deployment。

2.  如果存在，执行命令helm ls -a查看是否已经安装了应用。

3.  如果安装了应用，需要先删除应用，因为Helm v2与Helm v3数据不兼容。

    **说明：** Helm社区提供了Helm v2 to Helm v3插件，但是请慎重使用，以免出现数据丢失情况。Helm v2 to Helm v3插件信息，请参见[helm-2to3](https://github.com/helm/helm-2to3)。


## 升级步骤

1.  确保[前置检查](#section_wxa_b2o_q4p)通过。

2.  执行命令kubectl delete deploy tiller-deploy -n kube-system。

3.  下载[helm v3客户端](http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/pkg/helm/helm-v3.0.3-linux-amd64.tar.gz)安装新的应用。

    **说明：**

    安装新的应用前：

    -   需要将原有Helm v2安装的应用使用Helm v3重装一遍，请评估对业务的影响。
    -   重装应用会导致原有数据丢失，请注意数据备份与保护。

**相关文档**  


[Helm v3 Change log](https://github.com/helm/helm/releases/tag/v3.0.0)

[Helm v3与Helm v2的区别](https://v3.helm.sh/docs/faq/#changes-since-helm-2)

[Helm v2如何迁移到Helm v3](https://helm.sh/docs/topics/v2_v3_migration/)

