# 阿里云容器服务 Kubernetes 版免责声明 {#concept_265348 .concept}

容器服务旨在为用户提供稳定、可靠的企业级容器平台，但对于以下场景发生的问题，容器服务免责：

-   使用阿里云提供之外的第三方或开源软件引起风险造成的业务损失。
-   不规范操作，例如未通过容器服务升级集群版本、未通过容器服务修改 SLB 的配置、误删除资源等。
-   自行修改 Kubernetes 的关键配置，例如以下文件的路径、链接和内容。
    -   /var/lib/kubelet
    -   /var/lib/docker
    -   /etc/kubernetes
    -   /etc/kubeadm
-   模板中使用了Kubernetes集群预留的Annotation造成的资源不可用、申请失败、异常等影响。

    kubernetes.io/，k8s.io/开头的标签为核心组件预留标签。例如，pv.kubernetes.io/bind-completed: "yes"

-   在容器服务控制台通过接入已有集群功能接入外部Kubernetes集群时，由于外部集群与阿里云之间的网络不稳定，从而导致集群访问不稳定而造成的业务损失。
-   其他一切影响集群稳定运行的不当操作。

