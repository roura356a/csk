# 网络管理FAQ

本文汇总网络管理中容器网络、Service和Ingress的常见问题。

## 容器管理FAQ

-   [手动升级了Flannel镜像版本后，如何解决无法兼容1.16以上版本集群的问题？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/容器网络CNI/容器网络FAQ.md)
-   [如何解决Pod启动后存在时延的问题？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/容器网络CNI/容器网络FAQ.md)
-   [如何让Pod访问自己暴露的服务？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/容器网络CNI/容器网络FAQ.md)
-   [如何选择Kubernetes集群Terway和Flannel网络插件？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/容器网络CNI/容器网络FAQ.md)
-   [如何规划集群网络？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/容器网络CNI/容器网络FAQ.md)
-   [ACK是否支持hostPort的端口映射？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/容器网络CNI/容器网络FAQ.md)

## Service FAQ

**SLB相关**

-   [为什么看不到Service与LoadBalancer同步过程的事件Event信息？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/Service FAQ.md)
-   [SLB创建一直处于Pending状态如何处理？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/Service FAQ.md)
-   [SLB虚拟服务器组未更新如何处理？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/Service FAQ.md)
-   [Service注解不生效如何处理？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/Service FAQ.md)
-   [为何SLB的配置被修改？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/Service FAQ.md)
-   [为什么集群内无法访问SLB IP？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/Service FAQ.md)
-   [误删除SLB怎么办？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/Service FAQ.md)
-   [删除Service是否会删除SLB？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/Service FAQ.md)
-   [旧版本CCM如何支持SLB重命名？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/Service FAQ.md)
-   [Local模式下如何自动设置Node权重？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/Service FAQ.md)

**CCM升级相关**

-   [CCM升级失败如何处理？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/Service FAQ.md)
-   [为何ACK控制台无法查看CCM升级检查报告？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/Service FAQ.md)

**使用已有SLB常见问题**

-   [为什么复用已有SLB没有生效？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/Service FAQ.md)
-   [为什么复用已有SLB时没有配置监听？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/Service FAQ.md)

**其他**

-   [如何将CCM接入至在阿里云ECS中自建的Kubernetes集群中？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/Service FAQ.md)

## Ingress FAQ

-   [Ingress支持哪些SSL/TLS版本？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/Ingress FAQ.md)
-   [Ingress L7请求头默认是透传的吗？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/Ingress FAQ.md)
-   [后端服务为HTTPS服务访问时是否可以通过Ingress-Nginx转发？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/Ingress FAQ.md)
-   [Ingress L7透传客户端IP吗？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/Ingress FAQ.md)
-   [nginx-ingress-controller组件支持HSTS吗？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/Ingress FAQ.md)
-   [Ingress-Nginx支持哪些Rewrite配置？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/Ingress FAQ.md)
-   [当版本升级后SLS解析日志不正常怎样修复？](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/Ingress FAQ.md)

