# 在阿里云容器服务Kubernetes上使用分批发布 {#task_hyt_s1r_s2b .task}

您可使用阿里云容器服务Kubernetes实现应用版本的分批发布，快速实现版本验证，支持应用快速迭代。

**说明：** Kubernetes最新集群已经默认安装alicloud-application-controller；对于旧版本的集群，目前仅支持1.9.3及以上的版本，您可通过控制台上的提示链接进行升级。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在Kubernetes菜单下，单击左侧导航栏中的**应用** \> **发布**，进入发布页面。
3.  在**分批发布**页签，单击右上角的**创建分批发布**。 

    **说明：** 如果按钮是灰色的，可以参考升级连接进行升级。

4.  配置分批发布信息，包括应用名称、部署集群、命名空间和发布选项。最后单击**下一步**。 

    ![配置分批发布信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15690636488982_zh-CN.png)

5.  在分批发布配置页面，配置后端的Pod和Service，最后单击**更新**，创建应用。 

    ![更新配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15690636489004_zh-CN.png)

6.  返回发布列表，您可看到下面出现一个应用，状态显示为**未开始**，单击右侧的**详情**。 

    ![发布详情](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15690636489005_zh-CN.png)

7.  在应用详情页面中，您可查看更多信息，单击页面右上角的**新的变更**，进行一次分批发布的变更。
8.  在变更配置页面中，进行新版本应用的变更配置，完成后单击**更新**。 

    ![变更配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15690636489011_zh-CN.png)

9.  默认返回发布列表页面，您可查看该应用的分批发布状态，完成第一批次部署后，单击**详情**。 

    ![详情页面2](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15690636489014_zh-CN.png)

10. 进入应用详情页面。您可看到未开始列表下的Pod为2个，已完成列表下的Pod为2个，表示分批发布第一个批次已完成。单击**继续**，可发布第二批Pod，单击**回滚**，此时会回滚到之前的版本。 

    ![回滚](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15690636489013_zh-CN.png)

11. 当发布完成后。单击**历史**，可以进行历史版本的回滚。 

    ![历史回滚](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17337/15690636489015_zh-CN.png)


您可使用分批发布功能实现应用版本的快速验证，没有流量损失，与蓝绿发布相比更节省资源，目前暂时只支持页面操作，后续会开放yaml文件编辑，支持更复杂的操作。

