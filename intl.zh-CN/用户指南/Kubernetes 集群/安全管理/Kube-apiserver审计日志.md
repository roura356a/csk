# Kube-apiserver审计日志 {#concept_svk_cgf_2fb .concept}

在Kubernetes集群中，apiserver的审计日志可以帮助集群管理人员记录或追溯不同用户的日常操作，是集群安全运维中重要的环节。本文旨在帮助您了解阿里云Kubernetes集群apiserver审计日志的相关配置，以及如何通过SLS日志服务收集和搜索指定的日志内容。

## 配置介绍 {#section_edv_5gf_2fb .section}

当前创建Kubernetes集群会默认开启apiserver审计功能，相关的参数配置功能如下：

**说明：** 登录到Master节点，apiserver配置文件的目录是/etc/kubernetes/manifests/kube-apiserver.yaml。

|配置|说明|
|--|--|
|audit-log-maxbackup|审计日志最大分片存储10个日志文件|
|audit-log-maxsize|单个审计日志最大size为100MB|
|audit-log-path|审计日志输出路径为/var/log/kubernetes/kubernetes.audit|
|audit-log-maxage|审计日志最多保存期为7天|
|audit-policy-file|审计日志配置策略文件，文件路径为：/etc/kubernetes/audit-policy.yml|

登录Master节点机器，审计配置策略文件的目录是/etc/kubernetes/audit-policy.yml，内容如下：

```
apiVersion: audit.k8s.io/v1beta1 # This is required.
kind: Policy
# Don't generate audit events for all requests in RequestReceived stage.
omitStages:
  - "RequestReceived"
rules:
  # The following requests were manually identified as high-volume and low-risk,
  # so drop them.
  - level: None
    users: ["system:kube-proxy"]
    verbs: ["watch"]
    resources:
      - group: "" # core
        resources: ["endpoints", "services"]
  - level: None
    users: ["system:unsecured"]
    namespaces: ["kube-system"]
    verbs: ["get"]
    resources:
      - group: "" # core
        resources: ["configmaps"]
  - level: None
    users: ["kubelet"] # legacy kubelet identity
    verbs: ["get"]
    resources:
      - group: "" # core
        resources: ["nodes"]
  - level: None
    userGroups: ["system:nodes"]
    verbs: ["get"]
    resources:
      - group: "" # core
        resources: ["nodes"]
  - level: None
    users:
      - system:kube-controller-manager
      - system:kube-scheduler
      - system:serviceaccount:kube-system:endpoint-controller
    verbs: ["get", "update"]
    namespaces: ["kube-system"]
    resources:
      - group: "" # core
        resources: ["endpoints"]
  - level: None
    users: ["system:apiserver"]
    verbs: ["get"]
    resources:
      - group: "" # core
        resources: ["namespaces"]
  # Don't log these read-only URLs.
  - level: None
    nonResourceURLs:
      - /healthz*
      - /version
      - /swagger*
  # Don't log events requests.
  - level: None
    resources:
      - group: "" # core
        resources: ["events"]
  # Secrets, ConfigMaps, and TokenReviews can contain sensitive & binary data,
  # so only log at the Metadata level.
  - level: Metadata
    resources:
      - group: "" # core
        resources: ["secrets", "configmaps"]
      - group: authentication.k8s.io
        resources: ["tokenreviews"]
  # Get repsonses can be large; skip them.
  - level: Request
    verbs: ["get", "list", "watch"]
    resources:
      - group: "" # core
      - group: "admissionregistration.k8s.io"
      - group: "apps"
      - group: "authentication.k8s.io"
      - group: "authorization.k8s.io"
      - group: "autoscaling"
      - group: "batch"
      - group: "certificates.k8s.io"
      - group: "extensions"
      - group: "networking.k8s.io"
      - group: "policy"
      - group: "rbac.authorization.k8s.io"
      - group: "settings.k8s.io"
      - group: "storage.k8s.io"
  # Default level for known APIs
  - level: RequestResponse
    resources:
      - group: "" # core
      - group: "admissionregistration.k8s.io"
      - group: "apps"
      - group: "authentication.k8s.io"
      - group: "authorization.k8s.io"
      - group: "autoscaling"
      - group: "batch"
      - group: "certificates.k8s.io"
      - group: "extensions"
      - group: "networking.k8s.io"
      - group: "policy"
      - group: "rbac.authorization.k8s.io"
      - group: "settings.k8s.io"
      - group: "storage.k8s.io"
  # Default level for all other requests.
  - level: Metadata
```

**说明：** 

-   在收到请求后不立即记录日志，当返回体header发送后才开始记录。
-   对于大量冗余的kube-proxy watch请求，kubelet和system:nodes对于node的get请求，kube组件在kube-system下对于endpoint的操作，以及apiserver对于namespaces的get请求等不作审计。
-   对于/healthz\*，/version\*, /swagger\*等只读url不作审计。
-   对于可能包含敏感信息或二进制文件的secrets，configmaps，tokenreviews接口的日志等级设为metadata，该level只记录请求事件的用户、时间戳、请求资源和动作，而不包含请求体和返回体。
-   对于一些如authenticatioin、rbac、certificates、autoscaling、storage等敏感接口，根据读写记录相应的请求体和返回体。

## 采集和检索 {#section_s4g_dhf_2fb .section}

在使用Kube-apiserver审计日志之前，请确保在创建集群的配置中开启了SLS日志服务，并成功创建对应的日志Project和Logstore。

1.  登录 [日志服务管理控制台](https://sls.console.aliyun.com/)。
2.  单击左侧导航栏中**Project管理**，选择创建集群时设置的日志Project，单击名称进入日志Project页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21467/154762674812111_zh-CN.png)

3.  在Project详情页中，默认进入日志库页面，查看名为audit-$\{clustered\}的日志库（logstore），单击右侧的**查询**，集群对应的审计日志会收集在该日志库中。

    **说明：** 在您创建过程中，指定的日志Project中会自动添加一个名称为audit-$\{clustereid\}的日志库。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21467/154762674812114_zh-CN.png)

4.  当集群管理员需要关注某一子账号的行为时，可以输入相应子账号id，追溯其操作。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21467/154762674812118_zh-CN.png)

5.  当集群管理员关注某一具体资源对象时，可以输入相应的资源名称，检索时间段内的指定操作。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21467/154762674812119_zh-CN.png)


## 支持第三方日志解决 {#section_nf2_lsf_2fb .section}

您可以在集群Master各节点，在/var/log/kubernetes/kubernetes.audit路径下找到审计日志的源文件。该文件是标准的json格式，您可以在部署集群时选择不使用阿里云的日志服务，根据需要对接其他的日志解决方案，完成相关审计日志的采集和检索。

