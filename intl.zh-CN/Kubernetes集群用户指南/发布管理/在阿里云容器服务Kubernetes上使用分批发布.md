# 在阿里云容器服务Kubernetes上使用分批发布

您可使用阿里云容器服务Kubernetes实现应用版本的分批发布，快速实现版本验证，支持应用快速迭代。

Kubernetes最新集群已经默认安装alicloud-application-controller；对于旧版本的集群，目前仅支持1.9.3及以上的版本，您可通过控制台上的提示链接进行升级。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，单击**发布**。

5.  选择**分批发布**页签，单击右上角的**创建分批发布**。

    **说明：** 如果按钮是灰色的，可以参见升级连接进行升级。

6.  配置分批发布信息，包括**应用名称**、**集群**、**命名空间**和**发布选项**。最后单击**下一步**。

    ![配置分批发布信息](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9295659951/p8982.png)

7.  在分批发布配置页面，配置后端的Pod和Service，最后单击**更新**，创建应用。

    ![更新配置](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9295659951/p9004.png)

8.  返回发布列表，您可看到下面出现一个应用，状态显示为**未开始**，单击右侧的**详情**。

    ![发布详情](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0395659951/p9005.png)

9.  在应用详情页面中，您可查看更多信息，单击页面右上角的**新的变更**，进行一次分批发布的变更。

10. 在变更配置页面中，进行新版本应用的变更配置，完成后单击**更新**。

    ![变更配置](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0395659951/p9011.png)

11. 默认返回发布列表页面，您可查看该应用的分批发布状态，完成第一批次部署后，单击**详情**。

    ![详情页面2](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0395659951/p9014.png)

12. 在应用**详情**页面，您可看到未开始列表下的Pod为2个，已完成列表下的Pod为2个，表示分批发布第一个批次已完成。单击**继续**，可发布第二批Pod。单击**回滚**，此时会回滚到之前的版本。

    ![回滚](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0395659951/p9013.png)

13. 当发布完成后。单击**历史**页签，可以进行历史版本的回滚。

    ![历史回滚](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0395659951/p9015.png)


您可使用分批发布功能实现应用版本的快速验证，没有流量损失，与蓝绿发布相比更节省资源，目前暂时只支持页面操作，后续会开放yaml文件编辑，支持更复杂的操作。

