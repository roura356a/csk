# 网络管理FAQ

本文汇总网络管理中容器网络、Service和Ingress的常见问题。

## 容器网络FAQ

-   [Terway网络模式下增加了虚拟交换机后，集群无法访问公网怎么办？](/intl.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/容器网络FAQ.md)
-   [手动升级了Flannel镜像版本后，如何解决无法兼容1.16以上版本集群的问题？](/intl.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/容器网络FAQ.md)
-   [如何解决Pod启动后存在时延的问题？](/intl.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/容器网络FAQ.md)
-   [如何让Pod访问自己暴露的服务？](/intl.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/容器网络FAQ.md)
-   [如何选择Kubernetes集群Terway和Flannel网络插件？](/intl.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/容器网络FAQ.md)
-   [如何规划集群网络？](/intl.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/容器网络FAQ.md)
-   [ACK是否支持hostPort的端口映射？](/intl.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/容器网络FAQ.md)
-   [ACK集群是否支持VPC多路由表？](/intl.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/容器网络FAQ.md)
-   [如何查看集群的网络类型及对应的虚拟交换机？](/intl.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/容器网络FAQ.md)
-   [如何查看集群中使用的云资源？](/intl.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/容器网络FAQ.md)
-   [Kubernetes集群中Pod出现网络异常](~~142373~~)
-   [如何确认Kubernetes集群内应用的出网地址](~~142274~~)
-   [使用Terway网络的ENI模式出现网络异常](~~147426~~)
-   [Kubernetes管控集群链路异常的排查思路](~~149275~~)
-   [Terway网络场景中交换机的IP资源不足](~~189784~~)
-   [Kubernetes集群中访问LoadBalancer暴露出去的SLB地址不通](~~171437~~)

## Service FAQ

**SLB相关**

-   [为什么看不到Service与LoadBalancer同步过程的事件Event信息？](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)
-   [SLB创建一直处于Pending状态如何处理？](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)
-   [SLB虚拟服务器组未更新如何处理？](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)
-   [Service注解不生效如何处理？](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)
-   [为何SLB的配置被修改？](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)
-   [为什么集群内无法访问SLB IP？](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)
-   [误删除SLB怎么办？](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)
-   [删除Service是否会删除SLB？](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)
-   [旧版本CCM如何支持SLB重命名？](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)
-   [Local模式下如何自动设置Node权重？](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)

**CCM升级相关**

-   [CCM升级失败如何处理？](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)

**使用已有SLB常见问题**

-   [为什么复用已有SLB没有生效？](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)
-   [为什么复用已有SLB时没有配置监听？](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)

**其他**

-   [Kubernetes的Service如何进行会话保持？](~~149276~~)

## Ingress FAQ

-   [Ingress支持哪些SSL/TLS版本？](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress FAQ.md)
-   [Ingress L7请求头默认是透传的吗？](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress FAQ.md)
-   [后端服务为HTTPS服务访问时是否可以通过Ingress-Nginx转发？](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress FAQ.md)
-   [Ingress L7透传客户端IP吗？](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress FAQ.md)
-   [nginx-ingress-controller组件支持HSTS吗？](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress FAQ.md)
-   [Ingress-Nginx支持哪些Rewrite配置？](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress FAQ.md)
-   [当版本升级后SLS解析日志不正常怎样修复？](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress FAQ.md)
-   [t1860692.dita\#task\_2401024](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/部署Ingress Controller使用私网SLB.md)
-   [在ACK组件管理中升级Nginx Ingress Controller组件时，系统所做的更新是什么？](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress FAQ.md)
-   [ingress-nginx如何使用已有的SLB？](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress FAQ.md)
-   [如何将Ingress-nginx的监听由四层改为七层（HTTPS/HTTP）？](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress FAQ.md)
-   [创建Ingress时报错“certificate signed by unknown authority”](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress FAQ.md)

