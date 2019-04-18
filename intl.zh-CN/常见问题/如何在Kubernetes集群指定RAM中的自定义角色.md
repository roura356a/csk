# 如何在Kubernetes集群指定RAM中的自定义角色 {#concept_wdt_bpx_3hb .concept}

Kubernetes集群暂不支持指定RAM中的自定义角色，但是在Kubernetes集群中创建worker时会自动生成一个角色，您可以通过给该角色添加policy的方式，来实现zh-CN角色授权。

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/)。
2.  在 Kubernetes 菜单下，选择**集群** \> **集群**，在集群列表页面，单击目标集群。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/155023/155554987244670_zh-CN.png)

3.  进入基本信息页面，在**集群资源**区域，单击Worker RAM 角色。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/155023/155554987344686_zh-CN.png)

4.  跳转到RAM控制台页面，在RAM 角色管理页面，单击权限策略名称。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/155023/155554987344693_zh-CN.png)

5.  进入策略权限管理页面，单击**修改策略内容**，将如下内容填入后，单击**确定**。本示例中是给该角色授予伸缩和删除集群的权限，如果您想了解更多的权限，请参见[表 1](../../../../intl.zh-CN/用户指南/Kubernetes集群/授权管理/创建自定义授权策略.md#table_pzw_5s2_xdb)。

    ```
    {
                "Action": [
                  "cs:ScaleCluster",
                  "cs:DeleteCluster"
                ],
                "Resource": "*",
                "Effect": "Allow"
             }
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/155023/155554987344694_zh-CN.png)


