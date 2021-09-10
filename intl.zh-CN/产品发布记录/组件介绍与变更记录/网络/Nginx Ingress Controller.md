---
keyword: Nginx Ingress Controller
---

# Nginx Ingress Controller

本文介绍Nginx Ingress Controller组件信息、使用说明和变更记录。

## 组件介绍

**Ingress基本概念**

在Kubernetes集群中，Ingress作为集群内服务对外暴露的访问接入点，其几乎承载着集群内服务访问的所有流量。Ingress是Kubernetes中的一个资源对象，用来管理集群外部访问集群内部服务的方式。您可以通过Ingress资源来配置不同的转发规则，从而达到根据不同的规则设置访问集群内不同的Service后端Pod。

**Ingress Controller工作原理**

为了使得Ingress资源正常工作，集群中必须要有个Ingress Controller来解析Ingress的转发规则。Ingress Controller收到请求，匹配Ingress转发规则转发到后端Service，而Service转发到Pod，最终由Pod处理请求。Kubernetes中Service、Ingress与Ingress Controller有着以下关系：

-   Service是后端真实服务的抽象，一个Service可以代表多个相同的后端服务。
-   Ingress是反向代理规则，用来规定HTTP/HTTPS请求应该被转发到哪个Service上。例如根据请求中不同的Host和URL路径，让请求落到不同的 Service上。
-   Ingress Controller是一个反向代理程序，负责解析Ingress的反向代理规则。如果Ingress有增删改的变动，Ingress Controller会及时更新自己相应的转发规则，当Ingress Controller收到请求后就会根据这些规则将请求转发到对应的Service。

## 使用说明

关于Nginx Ingress Controller的使用，请参见[通过Ingress实现灰度发布和蓝绿发布](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/通过Ingress实现灰度发布和蓝绿发布.md)和[通过Ingress Controller来实现应用的流量复制](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/通过Ingress Controller来实现应用的流量复制.md)。

## 变更记录

**2021年09月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.44.0.5-e66e17ee3-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.44.0.5-e66e17ee3-aliyun|2021年09月06日|-   升级AHAS sentinel插件。
    -   优化插件性能和稳定性。
    -   支持集群流控。
-   修复CVE-2021-36159漏洞。更多信息，请参见[CVE-2021-36159](https://nvd.nist.gov/vuln/detail/CVE-2021-36159)。
-   默认关闭kernel.core\_uses\_pid内核参数，防止coredump占用大量磁盘空间。

|建议在业务低峰期升级，变更过程中可能会导致已经建立的连接发生瞬断。|

**2021年06月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.44.0.3-8e83e7dc6-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.44.0.3-8e83e7dc6-aliyun|2021年06月01日|修复CVE-2021-23017漏洞。更多信息，请参见[Updating NGINX for a DNS Resolver Vulnerability \(CVE-2021-23017\)](https://www.nginx.com/blog/updating-nginx-dns-resolver-vulnerability-cve-2021-23017/)。|建议在业务低峰期升级，变更过程中可能会导致已经建立的连接发生瞬断。|

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.44.0.2-abf1c6fe4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.44.0.2-abf1c6fe4-aliyun|2021年04月01日|兼容老版本（0.30及之前的版本）log\_format中配置的`the_real_ip`字段。|建议在业务低峰期升级，变更过程中可能会导致已经建立的连接发生瞬断。|

**2021年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.44.0.1-5e842447b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.44.0.1-5e842447b-aliyun|2021年03月08日|-   默认开启Validating Admission Webhook。更多信息，请参见[How the NGINX Ingress controller works](https://kubernetes.github.io/ingress-nginx/how-it-works/#avoiding-outage-from-wrong-configuration)。
-   针对`service-weight`这个注解配置的值做合法性检查。
-   长连接和短连接性能提升20%~50%。
-   支持OCSP stapling。
-   升级LuaJIT到2.1.0版本。
-   升级Nginx到1.19.6版本。
-   升级Alpine镜像到3.13版本。
-   修复OpenSSL CVE。
-   默认启用TLS 1.3版本。

**说明：** HTTPS默认只支持TLS 1.2和TLS 1.3版本。如果需要支持TLS 1.0和TLS 1.1版本，请参见[Ingress支持哪些SSL/TLS版本？](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress FAQ.md)。

-   要求Kubernetes版本为v1.16及以上。
-   同步更新到社区0.44.0版本。更多信息，请参见[社区Changelog](https://github.com/kubernetes/ingress-nginx/blob/master/Changelog.md)。

|建议在业务低峰期升级，变更过程中可能会导致已经建立的连接发生瞬断。|

**2020年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.30.0.1-5f89cb606-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.30.0.1-5f89cb606-aliyun|2020年04月02日|-   新增FastCGI Backend支持。
-   默认启用Dynamic SSL Cert Update模式。
-   新增流量Mirror配置支持。
-   升级Nginx版本到1.17.8，OpenResty版本到1.15.8，更新基础镜像为Alpine。
-   新增Ingress Validating Webhook支持。
-   修复CVE-2018-16843、CVE-2018-16844、CVE-2019-9511、CVE-2019-9513和CVE-2019-9516漏洞。
-   重大更新如下：
    -   lua-resty-waf、session-cookie-hash、force-namespace-isolation等配置被废弃。
    -   x-forwarded-prefix类型从boolean转成string类型。
    -   log-format中的the\_real\_ip变量在下个版本将被废弃，统一采用remote\_addr替代。
-   同步更新到社区0.30.0版本，更多详细变更记录请参见[社区Changelog](https://github.com/kubernetes/ingress-nginx/blob/master/Changelog.md)。

|建议在业务低峰期升级，变更过程中可能会导致已经建立的连接发生瞬断。|

**2019年10月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.22.0.5-552e0db-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.22.0.5-552e0db-aliyun|2019年10月24日|开启Server动态更新时支持泛域名、白名单和重定向配置。|建议在业务低峰期升级，变更过程中可能会导致已经建立的连接发生瞬断。|

**2019年07月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.22.0.4-5a14d4b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.22.0.4-5a14d4b-aliyun|2019年07月18日|优化灰度发布规则，支持Perl正则匹配方式。|建议在业务低峰期升级，变更过程中可能会导致已经建立的连接发生瞬断。|

**2019年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.22.0.3-da10b7f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.22.0.3-da10b7f-aliyun|2019年04月25日|-   同步更新到社区0.22.0版本，变更记录请参见[Ingress-Nginx](https://github.com/kubernetes/ingress-nginx/releases)。
-   开启动态更新时支持蓝绿发布和灰度发布机制。
-   默认开启Nginx Upstream的动态更新特性。
-   重大更新如下：rewrite-target注释采用capture group配置形式，配置方式请参见[rewrite-target](https://kubernetes.github.io/ingress-nginx/examples/rewrite/#rewrite-target)，平滑升级方式请参见[Github](https://github.com/kubernetes/ingress-nginx/pull/3174#issuecomment-455665710)。

|建议在业务低峰期升级，变更过程中可能会导致已经建立的连接发生瞬断。|

**2019年01月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.20.0.2-cc39f1b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.20.0.2-cc39f1b-aliyun|2019年01月17日|-   优化默认的Nginx Worker进程数量配置，防止过多Nginx进程占用宿主机资源。
-   优化蓝绿发布和灰度发布时允许新老版本服务配置不同的服务端口号。
-   解决灰度发布过程中，新版本服务后端无Active Pod时，Nginx配置测试失败的问题。
-   修复因K8s API Server连接异常而导致Ingress Address端点不更新的问题。

|建议在业务低峰期升级，变更过程中可能会导致已经建立的连接发生瞬断。|

**2018年11月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.20.0.1-4597ce2-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.20.0.1-4597ce2-aliyun|2018年11月29日|-   同步更新到社区0.20.0版本，变更记录请参见[社区](https://github.com/kubernetes/ingress-nginx/releases)。
-   升级Nginx版本到1.15.6，修复HTTP/2相关安全漏洞。
-   Path支持正则表达式配置。
-   移除默认的default-http-backend服务，同时支持配置自定义默认后端服务。
-   支持基于IP、User-Agent和Referer的黑名单配置。
-   优化默认运行权限，剔除privileges运行权限。
-   支持AJP协议。

|建议在业务低峰期升级，变更过程中可能会导致已经建立的连接发生瞬断。|

