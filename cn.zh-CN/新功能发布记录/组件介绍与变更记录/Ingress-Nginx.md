# Ingress-Nginx

本文为您介绍Ingress-Nginx相关内容的最新动态。

## 2021年03月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.44.0.1-5e842447b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.44.0.1-5e842447b-aliyun|2021年03月08日|-   默认开启Validating Admission Webhook。
-   针对`service-weight`这个注解配置的值做合法性检查。
-   长连接和短连接性能提升20%~50%。
-   集成AHAS Sentinel能力。
-   支持OCSP stapling。
-   升级LuaJIT到2.1.0。
-   升级Nginx到1.19.6。
-   升级Alpine镜像到3.13。
-   修复OpenSSL CVE。
-   默认启用TLSv1.3。
-   要求Kubernetes版本为v1.16及以上。
-   同步更新到社区0.44.0版本。更多信息，请参见[社区Changelog](https://github.com/kubernetes/ingress-nginx/blob/master/Changelog.md)。

|建议在业务低峰期升级，变更过程中可能会导致已经建立的连接发生瞬断。|

## 2020年04月

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

## 2019年10月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.22.0.5-552e0db-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.22.0.5-552e0db-aliyun|2019年10月24日|开启Server动态更新时支持泛域名、白名单和重定向配置。|建议在业务低峰期升级，变更过程中可能会导致已经建立的连接发生瞬断。|

## 2019年07月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.22.0.4-5a14d4b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.22.0.4-5a14d4b-aliyun|2019年07月18日|优化灰度发布规则，支持Perl正则匹配方式。|建议在业务低峰期升级，变更过程中可能会导致已经建立的连接发生瞬断。|

## 2019年04月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.22.0.3-da10b7f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.22.0.3-da10b7f-aliyun|2019年04月25日|-   同步更新到社区0.22.0版本，变更记录请参见[Ingress-Nginx](https://github.com/kubernetes/ingress-nginx/releases)。
-   开启动态更新时支持蓝绿发布和灰度发布机制。
-   默认开启Nginx Upstream的动态更新特性。
-   重大更新如下：rewrite-target注释采用capture group配置形式，配置方式请参见[rewrite-target](https://kubernetes.github.io/ingress-nginx/examples/rewrite/#rewrite-target)，平滑升级方式请参见[Github](https://github.com/kubernetes/ingress-nginx/pull/3174#issuecomment-455665710)。

|建议在业务低峰期升级，变更过程中可能会导致已经建立的连接发生瞬断。|

## 2019年01月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.20.0.2-cc39f1b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.20.0.2-cc39f1b-aliyun|2019年01月17日|-   优化默认的Nginx Worker进程数量配置，防止过多Nginx进程占用宿主机资源。
-   优化蓝绿发布和灰度发布时允许新老版本服务配置不同的服务端口号。
-   解决灰度发布过程中，新版本服务后端无Active Pod时，Nginx配置测试失败的问题。
-   修复因K8s API Server连接异常而导致Ingress Address端点不更新的问题。

|建议在业务低峰期升级，变更过程中可能会导致已经建立的连接发生瞬断。|

## 2018年11月

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

