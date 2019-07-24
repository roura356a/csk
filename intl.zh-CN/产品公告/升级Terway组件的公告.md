# 升级Terway组件的公告 {#concept_262573 .concept}

-   v1.0.9.15-g3957085-aliyun

    修复了偶发的升级失败的问题。

-   v1.0.9.14-ga0346bb-aliyun
    -   修复Terway获取弹性网卡信息时偶发性失败的问题。
    -   修复创建容器时上报**failed to move veth to host netns: file exists**的问题。
    -   新增对弹性网卡状态定期扫描，对于异常释放的弹性网卡会定期回收的功能。
    -   优化健康检查：Terway健康检查方式从HTTP路径检查优化成TCP端口检查。

请前往容器服务控制台升级最新的Terway系统组件，此次升级不会对业务造成影响。

