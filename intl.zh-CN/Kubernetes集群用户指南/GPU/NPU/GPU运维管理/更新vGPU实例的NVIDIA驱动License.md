---
keyword: [vGPU机型已加入ACK集群, vGPU机型未加入ACK集群, 更新vGPU实例License]
---

# 更新vGPU实例的NVIDIA驱动License

Worker节点支持实例规格为vGPU的实例（即轻量级GPU实例）。使用vGPU实例需要购买NVIDIA官方提供的License，如果您已购买License，请根据本文描述的相应场景更新vGPU实例License。

## 场景一：vGPU实例已加入ACK集群

**说明：**

-   如果您还没有购买相应的License，请通过以下链接购买：[NVIDIA License购买](https://enterpriseproductregistration.nvidia.com/?LicType=EVAL&ProductFamily=vGPU)。
-   本文适用的ACK集群包括专有版、托管版和Pro版。

1.  登录vGPU节点，修改/etc/nvidia/gridd.conf文件中以下字段的值。

    **说明：** 关于如何登录到GPU节点，请参见[通过密码认证登录Linux实例](/intl.zh-CN/实例/连接实例/使用VNC连接实例/通过密码认证登录Linux实例.md)和[通过密码认证登录Windows实例](/intl.zh-CN/实例/连接实例/使用VNC连接实例/通过密码认证登录Windows实例.md)。

    ```
    ServerAddress=<your License Server address>
    ServerPort=<License Server port>
    ```

2.  执行以下命令重启vGPU。

    ```
    systemctl daemon-reload
    systemctl restart nvidia-gridd
    ```

3.  执行以下命令，确认该命令是否能正常执行。

    ```
    nvidia-smi
    ```

    预期输出：

    ```
    Wed Jun  2 14:22:29 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 450.102.04   Driver Version: 450.102.04   CUDA Version: 11.0     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00000000:00:07.0 Off |                    0 |
    | N/A   28C    P8     8W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    ```

    从上述输出可知，`nvidia-smi`命令可以被正常执行，说明您已成功更新该vGPU实例的NVIDIA驱动License。


## 场景二：vGPU实例未加入ACK集群

如果您的vGPU实例未添加到ACK集群中，您首先需要通过自动添加已有节点或手动添加已有节点的方式，将该vGPU实例加入到集群中，然后按照本文场景一描述的操作步骤更新vGPU的License。

关于如何将实例添加至集群，请参见[添加已有节点](/intl.zh-CN/Kubernetes集群用户指南/节点与节点池/节点/添加已有节点.md)。

