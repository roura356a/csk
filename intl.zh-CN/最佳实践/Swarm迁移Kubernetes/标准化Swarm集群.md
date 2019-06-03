# 标准化Swarm集群 {#concept_303905 .concept}

从集群外部访问应用时，如果您通过<HostIP\>:<port\>访问应用或基于备案域名但直接解析到NodeIP场景，则在迁移Swarm集群时，无法做到不停机迁移业务和实时回滚，所以先调整成统一通过SLB访问应用，再通过部署监控插件的方式，完成标准化Swarm集群，确保集群迁移时不停机迁移业务和实时回滚。

## 统一通过SLB访问Swarm集群 {#section_8rf_ooe_tan .section}

1.  创建SLB实例
    -   自动创建 SLB

        如果您在创建Swarm集群时，已勾选**自动创建负载均衡**，如[图 1](#fig_l3g_0im_yi0)所示。表示您已自动创建一个按量付费的负载均衡实例。

        ![](../DNcsk1877477/images/47879_zh-CN.png "设置SLB")

        您可以登录[负载均衡管理控制台](https://slbnext.console.aliyun.com/)，查看已创建的SLB，确认该SLB已配置了监听后端服务器的 TCP 9080 端口，如[图 2](#fig_7ch_mt5_iem)所示。

        ![](../DNcsk1877477/images/47880_zh-CN.png "查看服务端口")

    -   手动创建SLB

        如果您创建Swarm集群时，未勾选**自动创建负载均衡**，则只能在集群创建完成后，手动创建SLB并做绑定，具体步骤如下：

        1.  创建SLB。请参见[创建负载均衡实例](../../../../../intl.zh-CN/用户指南/负载均衡实例/创建负载均衡实例.md#)。
        2.  为Swarm集群绑定SLB，保证后续该集群配置会自动更新SLB配置。请参见[为集群绑定和解绑负载均衡](../../../../../intl.zh-CN/用户指南/集群管理/为集群绑定和解绑负载均衡.md#)。
2.  配置简单路由

    原来通过<HostIP\>:<port\>访问应用，因将Host IP绑定在客户端，如后续出现Host 替换或扩容，只能通过升级客户端方式来访问新Host 服务；因此，建议您通过域名访问服务的方式。

    **说明：** 如果没有申请自定义域名，阿里云容器服务免费支持为每个应用或服务配置独立域名，格式为XXX.$cluster\_id.$region\_id.alicontainer.com，其中*XXX*域名前缀可以根据每个应用或服务自行定义。

    通过这种方式，可以将服务访问切换成通过域名访问（用户自定义域名或容器服务提供的测试域名均可）的形式，而不再依赖于ECS或SLB的<HostIP\>:<port\>访问应用这种方式；详细操作请参见[简单路由（支持 HTTP/HTTPS\)](../../../../../intl.zh-CN/用户指南/服务发现和负载均衡/简单路由（支持 HTTP__HTTPS).md#)。

3.  SLB访问业务功能测试

    您可以使用上个步骤配置的域名访问业务，测试业务功能是否正常，并观察SLB流量是否有流量进入。

    1.  使用http://<HostIP\>:<port\>访问业务。

        ![](../DNcsk1877477/images/47907_zh-CN.png "容器服务提供的域名")

        ![](../DNcsk1877477/images/47908_zh-CN.png "用户自定义的域名")

    2.  查看SLB监控是否有流量进入。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/249256/155954506447909_zh-CN.png)

        **说明：** 请完成业务功能测试，确认业务流量通过SLB访问域名的方式对业务无影响。

4.  修改流量解析入口。
    -   **修改DNS解析地址**：若原先客户端是通过域名方式访问服务的，请联系DNS服务商修改DNS解析，将原来Node Host IP修改成SLB IP。

        **说明：** 修改DNS解析地址后，各DNS服务商都有缓存，需要观察SLB监控看流量是否已切换完成。

    -   **修改客户端访问地址**：若原先客户端是通过Node Host IP访问的，请升级客户端，修改成基于域名访问的方式。

## 统一部署云监控（可选） {#section_jdm_aaa_h64 .section}

如果在创建集群时，没有勾选**在ECS节点上安装云监控插件**，则无法在云监控查看ECS实例的监控信息，不利于后续迁移时观察集群情况。例如下线Swarm集群时，无法通过云监控确认对应ECS机器是否还有流量。您可以通过如下操作，部署云监控。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/249256/155954506447912_zh-CN.png)

1.  查看Swarm集群节点ECS监控信息。

    1.  您可以登录[容器服务管理控制台](https://cs.console.aliyun.com)，在左侧菜单栏单击**节点**，在目标节点右侧单击**监控**，进入云监控入口链接页面。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/249256/155954506447913_zh-CN.png)

    2.  单击**节点监控**，在**监控信息**区域可以查看到该节点在某个时间区间内的详细监控信息。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/249256/155954506447914_zh-CN.png)

    如果未安装云监控插件，则无法查看ECS详细监控信息如内存、磁盘、TCP连接数等。您可以按照如下操作，ECS云监控插件。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/249256/155954506447915_zh-CN.png)

2.  在Swarm集群节点上手动安装ECS云监控插件。
    1.  登录[云监控控制台](https://cms-intl.console.aliyun.com)，在左侧导航栏选择**Dashborad** \> **主机监控**，在**实例列表**页签，勾选目标实例，并单击**升级插件或批量安装**，选择对应的ECS机器列表升级插件即可。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/249256/155954506447916_zh-CN.png)

    2.  安装完成后，可以在**Dashborad** \> **主机监控**中，看到网络监控指标中，该网络设备的网络流入流出速率的曲线图。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/249256/155954506447917_zh-CN.png)


