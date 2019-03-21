# 使用日志服务进行Kubernetes日志采集 {#concept_nsj_sxm_t2b .concept}

阿里云容器服务Kubernetes集群集成了日志服务（SLS），您可在创建集群时启用日志服务，快速采集Kubernetes 集群的容器日志，包括容器的标准输出以及容器内的文本文件。

## 新建 Kubernetes 集群 {#section_ohf_y5r_gfb .section}

如果您尚未创建任何的 Kubernetes 集群，可以按照本节的步骤来进行操作：

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏中**集群**，单击右上角**创建Kubernetes集群**。
3.  进入创建页面后，参见[创建Kubernetes集群](../../../../../intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)进行配置。
4.  拖动到页面底部，勾选**日志服务**配置项，表示在新建的 Kubernetes 集群中安装日志插件。
5.  当勾选了使用日志服务后，会出现创建 Project（日志服务管理日志的组织结构，具体可见[项目](../../../../../intl.zh-CN/产品简介/基本概念/项目.md#)）的提示，目前有两种方式可选：
    -   选择一个现有的 Project 来管理采集的日志。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15531548969250_zh-CN.png)

    -   自动创建一个新的 Project 来管理采集的日志，Project 会自动命名为 `k8s-log-{ClusterID}`，ClusterID 表示您新建的 Kubernetes 集群的唯一标识。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15531548979251_zh-CN.png)

6.  配置完成后，单击右上角**创建集群**，在弹出的窗口中单击**确定**，完成创建。

    完成创建后，您可在集群列表页面看到创建的Kubernetes集群。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15531548979449_zh-CN.png)


## 已创建 Kubernetes 集群，手动安装日志服务组件 {#section_shf_y5r_gfb .section}

如果您先前已创建了 Kubernetes 集群，可以根据本节的内容来进行所需的操作以使用日志服务：

-   未安装：手动安装日志服务组件。
-   已安装但版本较老：升级日志服务组件，若不升级则只能使用日志服务控制台或 CRD 进行采集配置。

**检查日志服务组件版本**

1.  通过cloudshell 连接 Kubernetes 集群。

    详细内容，可参考[在CloudShell上通过kubectl管理Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/在CloudShell上通过kubectl管理Kubernetes集群.md#) 。

2.  快速判定是否需要进行升级或迁移操作，命令如下：

    ```
    $ kubectl describe daemonsets -n kube-system logtail-ds | grep ALICLOUD_LOG_DOCKER_ENV_CONFIG
    
    ```

    -   如果输出结果为 `ALICLOUD_LOG_DOCKER_ENV_CONFIG: true`，表示您可正常使用，不需要进行升级或迁移。
    -   其他情况则需要进行后续检查。
3.  执行以下检查命令确定是否是采用 helm 安装。

    ```
    $ helm get alibaba-log-controller | grep CHART
    CHART: alibaba-cloud-log-0.1.1
    
    ```

    -   输出内容中的 0.1.1 表示日志服务组件的版本，请使用 0.1.1 及以上版本的组件，如果版本过低，请参照[升级日志服务组件](#section_b3f_y5r_gfb)进行升级。如果您已采用 helm 安装且版本正确，可以跳过后续步骤。
    -   如果无任何内容输出，表示未采用 helm 安装日志服务组件，但可能采用了 DaemonSet 的方式安装，请根据后续步骤进行检查。
4.  DaemonSet 分为新旧两种方式：

    ```
    $ kubectl get daemonsets -n kube-system logtail
    
    ```

    -   如果无输出结果或内容是 `No resources found.`，则表明未安装日志服务组件，请参照操作[手动安装日志服务组件](#section_zhf_y5r_gfb)。
    -   如果有正确的输出结果表示使用旧 DaemonSet 方式进行了安装，需要进行升级，请参照[升级日志服务组件](#section_b3f_y5r_gfb)操作。

## 手动安装日志服务组件 {#section_zhf_y5r_gfb .section}

1.  通过cloudshell 连接 Kubernetes 集群。

    详细内容，可参考[在CloudShell上通过kubectl管理Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/在CloudShell上通过kubectl管理Kubernetes集群.md#) 。

2.  在cloudshell中获取您的主账号aliuid，命令为`echo $ALIBABA_CLOUD_ACCOUNT_ID`。
3.  替换`${your_k8s_cluster_id}`、`{your_ali_uid}`、`{your_k8s_cluster_region_id}`参数后执行以下安装命令。

    ```
    wget https://acs-logging.oss-cn-hangzhou.aliyuncs.com/alicloud-k8s-log-installer.sh -O alicloud-k8s-log-installer.sh; chmod 744 ./alicloud-k8s-log-installer.sh; ./alicloud-k8s-log-installer.sh --cluster-id ${your_k8s_cluster_id} --ali-uid ${your_ali_uid} --region-id ${your_k8s_cluster_region_id}
    ```

    **参数说明：**

    -   your\_k8s\_cluster\_id：您的Kubernetes集群ID。
    -   your\_ali\_uid：您在步骤[2](#)中获取的aliuid。
    -   your\_k8s\_cluster\_region\_id：您的Kubernetes集群所在的Region，可在[地域和可用区](https://www.alibabacloud.com/help/zh/doc-detail/40654.htm)中查看到，如在杭州，则为cn-hangzhou。

**安装示例**

```
[root@iZbp******biaZ ~]# wget https://acs-logging.oss-cn-hangzhou.aliyuncs.com/alicloud-k8s-log-installer.sh -O alicloud-k8s-log-installer.sh; chmod 744 ./alicloud-k8s-log-installer.sh; ./alicloud-k8s-log-installer.sh --cluster-id c77a*****************0106 --ali-uid 19*********19 --region-id cn-hangzhou
--2018-09-28 15:25:33--  https://acs-logging.oss-cn-hangzhou.aliyuncs.com/alicloud-k8s-log-installer.sh
Resolving acs-logging.oss-cn-hangzhou.aliyuncs.com... 118.31.219.217, 118.31.219.206
Connecting to acs-logging.oss-cn-hangzhou.aliyuncs.com|118.31.219.217|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2273 (2.2K) [text/x-sh]
Saving to: ‘alicloud-k8s-log-installer.sh’

alicloud-k8s-log-installer.sh                      100%[================================================================================================================>]   2.22K  --.-KB/s    in 0s

2018-09-28 15:25:33 (13.5 MB/s) - ‘alicloud-k8s-log-installer.sh’ saved [2273/2273]

--2018-09-28 15:25:33--  http://logtail-release-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/kubernetes/alibaba-cloud-log.tgz
Resolving logtail-release-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com... 118.31.219.49
Connecting to logtail-release-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com|118.31.219.49|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2754 (2.7K) [application/x-gzip]
Saving to: ‘alibaba-cloud-log.tgz’

alibaba-cloud-log.tgz                              100%[================================================================================================================>]   2.69K  --.-KB/s    in 0s

2018-09-28 15:25:34 (79.6 MB/s) - ‘alibaba-cloud-log.tgz’ saved [2754/2754]

[INFO] your k8s is using project : k8s-log-c77a92ec5a3ce4e64a1bf13bde1820106
NAME:   alibaba-log-controller
LAST DEPLOYED: Fri Sep 28 15:25:34 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1beta1/CustomResourceDefinition
NAME                                   AGE
aliyunlogconfigs.log.alibabacloud.com  0s

==> v1beta1/ClusterRole
alibaba-log-controller  0s

==> v1beta1/ClusterRoleBinding
NAME                    AGE
alibaba-log-controller  0s

==> v1beta1/DaemonSet
NAME        DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE SELECTOR  AGE
logtail-ds  2        2        0      2           0          <none>         0s

==> v1beta1/Deployment
NAME                    DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
alibaba-log-controller  1        1        1           0          0s

==> v1/Pod(related)
NAME                                     READY  STATUS             RESTARTS  AGE
logtail-ds-6v979                         0/1    ContainerCreating  0         0s
logtail-ds-7ccqv                         0/1    ContainerCreating  0         0s
alibaba-log-controller-84d8b6b8cf-nkrkx  0/1    ContainerCreating  0         0s

==> v1/ServiceAccount
NAME                    SECRETS  AGE
alibaba-log-controller  1        0s


[SUCCESS] install helm package : alibaba-log-controller success.

```

## 升级日志服务组件 {#section_b3f_y5r_gfb .section}

如果您已安装旧版本的日志服务组件（通过 helm 或 DaemonSet），可以按照以下操作进行升级或迁移。

**说明：** 通过cloudshell 连接 Kubernetes 集群。

详细内容可参考[在CloudShell上通过kubectl管理Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/在CloudShell上通过kubectl管理Kubernetes集群.md#) 。

Helm 升级（推荐）

1.  下载最新的日志服务组件 helm 包，命令如下：

    ```
    wget http://logtail-release-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/kubernetes/alibaba-cloud-log.tgz -O alibaba-cloud-log.tgz
    
    ```

2.  使用 helm upgrade 进行升级，命令如下：

    ```
    helm get values alibaba-log-controller --all > values.yaml && helm upgrade alibaba-log-controller alibaba-cloud-log.tgz --recreate-pods -f values.yaml
    
    ```


DaemonSet升级

若您不是使用helm的方式安装的日志组件，可通过修改DaemonSet模板的方式进行升级。若您的镜像账号为acs，请参考[镜像仓库](https://cr.console.aliyun.com/images/cn-hangzhou/acs/logtail)中最新的tag，将镜像中的tag更新至最新版本；若您的镜像账号为log-service，请参考[镜像仓库](https://cr.console.aliyun.com/images/cn-hangzhou/log-service/logtail)中最新的tag，将镜像中的tag更新至最新版本。

**说明：** 

-   用户升级tag后，如果Logtail没有滚动更新，您需要手动删除Logtail的Pod，触发强制更新。
-   查看Logtail是否运行在所有节点（包括master节点）上。若没有，请为Logtail设置[tolerations](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/)。

```
tolerations:
- operator: "Exists"
```

更多最新配置方式，请参考[最新helm包中的相关配置](http://logtail-release-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/kubernetes/alibaba-cloud-log.tgz)。

## DaemonSet 迁移 {#section_e3f_y5r_gfb .section}

此升级步骤适用于**检查日志服务组件版本**时结果为使用旧方式 DaemonSet 的用户，该方式不支持在容器服务中进行日志服务配置，您可按如下操作进行升级迁移：

1.  按照新版本的方式安装，安装命令最后新增一个参数为您之前 Kubernetes 集群使用的日志服务 Project 名。

    例如 Project 名为 k8s-log-demo，集群 ID 为 c12ba2028cxxxxxxxxxx6939f0b，则安装命令为：

    ```
    wget https://acs-logging.oss-cn-hangzhou.aliyuncs.com/alicloud-k8s-log-installer.sh -O alicloud-k8s-log-installer.sh; chmod 744 ./alicloud-k8s-log-installer.sh; ./alicloud-k8s-log-installer.sh --cluster-id c12ba2028cxxxxxxxxxx6939f0b --ali-uid 19*********19 --region-id cn-hangzhou --log-project k8s-log-demo
    
    ```

2.  安装成功后，进入[日志服务控制台](https://sls.console.aliyun.com)。
3.  在日志服务控制台，将相应 Project 以及 Logstore 下的历史采集配置应用到新的机器组 `k8s-group-${your_k8s_cluster_id}`。
4.  一分钟后，将历史采集配置从历史的机器组中解绑。
5.  观察日志采集正常后，可以选择删除之前安装的 logtail daemonset。

**说明：** 升级期间会有部分日志重复；CRD 配置管理方式只对使用 CRD 创建的配置生效（由于历史配置使用非 CRD 方式创建，因此历史配置不支持 CRD 管理方式）。

## 创建应用时配置日志服务 {#section_g3f_y5r_gfb .section}

在容器服务中，您可以在创建应用的同时配置日志服务对容器的日志进行采集，目前支持以控制台向导和 YAML 模板两种方式进行创建。

**控制台向导创建**

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **部署**，然后单击页面右上角的**使用镜像创建**。
3.  设置**应用名称**、**部署集群**、**命名空间**、**副本数量**和**类型**，单击**下一步**，进入容器配置页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15531548979451_zh-CN.png)

4.  进入容器配置页面中，本例中选择nginx镜像，对容器采集进行配置。

    以下仅介绍日志服务相关的配置，其他的应用配置可参见[使用镜像创建无状态Deployment应用](intl.zh-CN/用户指南/Kubernetes集群/应用管理/使用镜像创建无状态Deployment应用.md#)。

5.  进行**日志配置**。单击**+**号创建新的采集配置，每个采集配置由 **Logstore 名称**和**日志采集路径**两项构成。

    -   Logstore 名称：您可以使用它来指定所采集日志存储于哪个 Logstore，如果该 Logstore 不存在的话，我们会自动为您在集群关联的日志服务 Project 下创建相应的 Logstore。

        **说明：** 名称中不能包含下划线（\_），可以使用 - 来代替。

    -   日志采集路径：您可以用它来指定希望采集的日志所在的路径，如使用/usr/local/tomcat/logs/catalina.\*.log来采集tomcat的文本日志。

        **说明：** 如果指定为 stdout 则表示采集容器的标准输出和标准错误输出。

        每一项采集配置都会被自动创建为对应 Logstore 的一个采集配置，默认采用极简模式（按行）进行采集，如果您需要更丰富的采集方式，可以前往日志服务控制台，进入相应的 Project（默认是 k8s-log 前缀）和 Logstore 对配置进行修改。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15531548979460_zh-CN.png)

6.  自定义 Tag。单击**+**号创建新的自定义 Tag，每一个自定义 Tag 都是一个键值对，会拼接到所采集到的日志中，您可以使用它来为容器的日志数据进行标记，比如版本号。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15531548979473_zh-CN.png)

7.  当完成所有配置后，可单击右上角的**下一步**进入后续流程，后续操作可见[使用镜像创建无状态Deployment应用](intl.zh-CN/用户指南/Kubernetes集群/应用管理/使用镜像创建无状态Deployment应用.md#)。

**使用 YAML 模板创建**

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **部署**，然后单击页面右上角的**使用模板创建**。
3.  YAML 模板的语法同 Kubernetes 语法，但是为了给容器指定采集配置，需要使用 env 来为 container 增加**采集配置**和**自定义 Tag**，并根据采集配置，创建对应的 volumeMounts 和 volumns。以下是一个简单的 Pod 示例：

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: my-demo
    spec:
      containers:
      - name: my-demo-app
        image: 'registry.cn-hangzhou.aliyuncs.com/log-service/docker-log-test:latest'
        env:
        ######### 配置 环境变量 ###########
        - name: aliyun_logs_log-stdout
          value: stdout
        - name: aliyun_logs_log-varlog
          value: /var/log/*.log
        - name: aliyun_logs_mytag1_tags
          value: tag1=v1
        ###############################
        ######### 配置vulume mount ###########
        volumeMounts:
        - name: volumn-sls-mydemo
          mountPath: /var/log
      volumes:
      - name: volumn-sls-mydemo
        emptyDir: {}
      ###############################
    ```

    -   其中有三部分需要根据您的需求进行配置，一般按照顺序进行配置。
    -   第一部分通过环境变量来创建您的**采集配置**和**自定义 Tag**，所有与配置相关的环境变量都采用 `aliyun_logs_` 作为前缀。
    -   创建采集配置的规则如下：

        ```
        - name: aliyun_logs_{Logstore 名称}
          value: {日志采集路径}
        
        ```

        示例中创建了两个采集配置，其中 `aliyun_logs_log-stdout` 这个 env 表示创建一个 Logstore 名字为 log-stdout，日志采集路径为 stdout 的配置，从而将容器的标准输出采集到 log-stdout 这个 Logstore 中。

        **说明：** Logstore 名称中不能包含下划线（\_），可以使用 - 来代替。

    -   **创建自定义 Tag** 的规则如下：

        ```
        - name: aliyun_logs_{任意不包含'_'的名称}_tags
          value: {Tag 名}={Tag 值}
        
        ```

        配置 Tag 后，当采集到该容器的日志时，会自动附加对应的字段到日志服务。

    -   如果您的采集配置中指定了非 stdout 的采集路径，需要在此部分创建相应的 volumnMounts。

        示例中采集配置添加了对/var/log/\*.log 的采集，因此相应地添加了/var/log的 volumeMounts。

4.  当 YAML 编写完成后，单击**创建**，即可将相应的配置交由 Kubernetes 集群执行。

## 查看日志 {#section_t3f_y5r_gfb .section}

本例中查看通过控制台向导创建的tomcat应用的日志。完成配置后，tomcat应用的日志已被采集并存储到日志服务中，您可以如下步骤来查看您的日志：

1.  安装成功后，进入[日志服务控制台](https://sls.console.aliyun.com)。
2.  在进入控制台后，选择 Kubernetes 集群对应的 Project（默认为 k8s-log-\{Kubernetes 集群 ID\}），进入 Logstore 列表页面。
3.  在列表中找到相应的 Logstore（采集配置中指定），单击**查询**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15531548979474_zh-CN.png)

4.  本例中，在日志查询页面，您可查看tomcat应用的标准输出日志和容器内文本日志，并可发现自定义tag附加到日志字段中。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17400/15531548979541_zh-CN.png)


## 更多信息 {#section_rg2_tdh_hfb .section}

1.  默认情况下，我们会使用极简模式来采集您的数据（按行采集、不解析），如果您需要更复杂的配置，可以参考以下日志服务文档并前往日志服务控制台进行配置修改：
    -   [容器文本日志](../../../../../intl.zh-CN/用户指南/Logtail采集/容器日志采集/容器文本日志.md#)
    -   [容器标准输出](../../../../../intl.zh-CN/用户指南/Logtail采集/容器日志采集/容器标准输出.md#)
2.  除了通过控制台配置采集以外，您还可以直接通过 CRD 配置来对 Kubernetes 集群进行日志采集。
3.  对于异常的排查，可以参考[排查日志采集异常](../../../../../intl.zh-CN/常见问题/日志采集/Logtail排查简介.md#)。

