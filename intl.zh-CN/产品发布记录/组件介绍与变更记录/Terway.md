---
keyword: Terway变更记录
---

# Terway

Terway是阿里云开源的基于专有网络VPC的容器网络接口CNI（Container Network Interface）插件，支持基于Kubernetes标准的网络策略来定义容器间的访问策略。您可以通过使用Terway网络组件实现Kubernetes集群内部的网络互通。本文为您介绍了Terway相关内容的最新动态。

## 背景信息

Terway网络插件是ACK自研的网络插件，将原生的弹性网卡分配给Pod实现Pod网络，支持基于Kubernetes标准的网络策略（Network Policy）来定义容器间的访问策略，并兼容Calico的网络策略。

在Terway网络插件中，每个Pod都拥有自己网络栈和IP地址。同一台ECS内的Pod之间通信，直接通过机器内部的转发，跨ECS的Pod通信、报文通过VPC的弹性网卡直接转发。由于不需要使用VxLAN等的隧道技术封装报文，因此Terway模式网络具有较高的通信性能。更多信息，请参见[使用Terway网络插件](/intl.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/使用Terway网络插件.md)。

## 2021年03月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.10.323-g778c128-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.323-g778c128-aliyun|2021年03月22日|-   优化对OpenAPI请求调用的次数。
-   优化检查安全组配置时的事件告警。

|此次升级不会对业务造成影响。 |

## 2021年02月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.10.317-g0652857-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.317-g0652857-aliyun|2021年02月22日|-   修复Cilium Identity资源泄漏问题。
-   修复偶现的有状态应用Pod IP管理问题。

|此次升级不会对业务造成影响。 |
|v1.0.10.309-g5314eee-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.309-g5314eee-aliyun|2021年02月05日|-   去除主动修复安全组规则的逻辑，只通过事件提示修复。
-   修复节点在高负载情况下偶现的Pod IP被回收又重新分配问题。

|此次升级不会对业务造成影响。 |

## 2021年01月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.10.301-g0115576-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.301-g0115576-aliyun|2021年01月21日|新增：

-   增加自定义MTU功能。
-   增加ENI独占模式下对主机网络栈路由的支持。

修复：

-   修复Terway限流功能产生丢包问题。
-   修复IP规则残留问题。
-   修复弹性网卡ENI IP数量计算错误问题。

|此次升级不会对业务造成影响。 |

## 2020年12月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.10.280-gdc2cb6c-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.280-gdc2cb6c-aliyun|2020年12月25日|-   增加Terway主动检查、修复ENI安全组的配置。
-   增加IPVLAN模式下对主机网络栈路由的支持。

|此次升级不会对业务造成影响。 |
|v1.0.10.263-gdbe50a9-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.263-gdbe50a9-aliyun|2020年12月03日|修复ENI独占模式下Terway异常的问题。|此次升级不会对业务造成影响。 |

## 2020年11月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.10.261-g8342155-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.261-g8342155-aliyun|2020年11月27日|-   支持使用同步调用方式调用ECS接口。
-   修复使用CNI插件时返回错误信息的问题。

|此次升级不会对业务造成影响。 |
|v1.0.10.250-gb7bb10a-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.250-gb7bb10a-aliyun|2020年11月23日|-   修复ENI驱动加载失败时的Pod网络问题。
-   修复ENI OpenAPI被流控时释放的IP的状态问题。

|此次升级不会对业务造成影响。 |

## 2020年10月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.10.247-g4cb77d0-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.247-g4cb77d0-aliyun|2020年10月26日|支持ECS DDH机型。|此次升级不会对业务造成影响。 |

## 2020年9月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.10.237-g6a0f948-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.237-g6a0f948-aliyun|2020年9月16日|优化了ENI的绑定速度。|此次升级不会对业务造成影响。 |

## 2020年8月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.10.221-g8d6386a-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.221-g8d6386a-aliyun|2020年8月11日|支持IPVLAN+eBPF的容器网络。（白名单功能，[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请使用。）|此次升级不会对业务造成影响。 |
|v1.0.10.213-g27145cc-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.213-g27145cc-aliyun|2020年8月4日|修复ENI网卡偶发无法使用导致Pod网络不通的问题。|此次升级不会对业务造成影响。 |

## 2020年7月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.10.208-gf3144bf-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.208-gf3144bf-aliyun|2020年7月20日|-   修复高密度节点策略路由泄露问题。
-   支持内网OpenAPI。
-   修复在VSwitch满时无法释放Pod IP的问题。
-   优化CNI失败的报错信息展示。

|此次升级不会对业务造成影响。 |
|v1.0.10.211-gef088a4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.211-gef088a4-aliyun|2020年7月24日|给ENI打上集群ID的Tag。|此次升级不会对业务造成影响。 |

## 2020年4月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.10.156-g8660a0f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.156-g8660a0f-aliyun|2020年4月22日|-   优化弹性网卡（Elastic Network Interface，简称ENI）网络资源的缓存效率。
-   升级内置Felix到3.5.8版本。
-   增加对Completed\|Failed状态的Pod网络资源回收。

|此次升级不会对业务造成影响。 |

## 2020年2月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.10.139-g14a4f84-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.139-g14a4f84-aliyun|2020年2月12日|修复偶发Pod创建超时的问题。|此次升级不会对业务造成影响。|

## 2020年1月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.10.133-g001396b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.133-g001396b-aliyun|2020年1月10日|-   支持关闭NetworkPolicy。
-   ENI多IP集群支持使用IPVlan作为Pod网络虚拟化技术。

|此次升级不会对业务造成影响。|

## 2019年12月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.10.122-gd0be015-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.122-gd0be015-aliyun|2019年12月24日|优化ENI多IP的分配效率。|此次升级不会对业务造成影响。|

## 2019年10月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.10.100-g92a3fa5-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.100-g92a3fa5-aliyun|2019年10月11日|修复在大量Job并发调用时导致宿主节点NotReady的问题。|此次升级不会对业务造成影响。|

## 2019年8月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.9.20-g35ae000-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.20-g35ae000-aliyun|2019年8月23日|兼容Kubernetes 1.14.6。|此次升级不会对业务造成影响。|

## 2019年4月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.9.15-g3957085-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.15-g3957085-aliyun|2019年4月11日|修复Terway组件升级过程中偶发性失败的问题。|此次升级不会对业务造成影响。|

## 2019年3月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.9.14-ga0346bb-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.14-ga0346bb-aliyun|2019年3月28日|-   修复Terway在meta server被流控时获取弹性网卡信息失败的问题。
-   修复创建容器时上报**failed to move veth to host netns: file exists**的问题。
-   新增对弹性网卡状态的定期扫描，对于异常释放的弹性网卡会定期回收的功能。
-   优化健康检查：Terway健康检查方式从HTTP路径检查优化成TCP端口检查。

|此次升级不会对业务造成影响。|

**相关文档**  


[使用Terway网络插件](/intl.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/使用Terway网络插件.md)

