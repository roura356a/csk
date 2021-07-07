---
keyword: [vGPU实例, GPU虚拟化机型, 更新vGPU实例的NVIDIA驱动License]
---

# 更新ACK集群中GPU虚拟化型（vGPU）实例的NVIDIA驱动License

如果集群中存在vGPU实例类型的节点，您需要购买NVIDIA官方提供的GRID License，且自建License服务器该节点才能正常工作。本文介绍如何更新ACK集群中vGPU实例的NVIDIA驱动License。

-   购买相应的License，更多信息，请参见[NVIDIA License购买](https://enterpriseproductregistration.nvidia.com/?LicType=EVAL&ProductFamily=vGPU)。
-   本文适用的ACK集群包括专有版、托管版和Pro版。

## vGPU实例已加入ACK集群

License服务器搭建完成后，按照以下步骤更新节点GRID License。

**说明：** 请购买ECS实例并参考NVIDIA官网教程搭建License服务器。更多信息，请参见[NVIDIA](https://www.nvidia.com/object/nvidia-enterprise-account.html?spm=a2c4g.11186623.2.10.3fac7d8fHWP47f)。

1.  登录vGPU节点，修改/etc/nvidia/gridd.conf文件中以下字段的值。

    **说明：** 关于如何登录到GPU节点，请参见[通过密码认证登录Linux实例](/intl.zh-CN/实例/连接实例/使用VNC连接实例/通过密码认证登录Linux实例.md)和[通过密码认证登录Windows实例](/intl.zh-CN/实例/连接实例/使用VNC连接实例/通过密码认证登录Windows实例.md)。

    ```
    ServerAddress=<your License Server address>
    ServerPort=<License Server port>
    ```

2.  执行以下命令重启NVIDIA驱动。

    ```
    systemctl daemon-reload
    systemctl restart nvidia-gridd
    ```

3.  执行以下命令，确认该命令是否能正常执行。

    ```
    nvidia-smi
    ```

    预期输出：

    ![222](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2920755261/p291941.png)

    从上述输出可知，`nvidia-smi`命令可以被正常执行，说明您已成功更新该vGPU实例的NVIDIA驱动License。


