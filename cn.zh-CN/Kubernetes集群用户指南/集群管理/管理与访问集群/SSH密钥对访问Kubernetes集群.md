# SSH密钥对访问Kubernetes集群 {#task_hq3_4cr_b2b .task}

阿里云容器服务支持通过 SSH 密钥对的方式登录集群，保障远程SSH访问的安全性。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**集群** \> **集群**，进入 Kubernetes 集群列表页面。
3.  单击右上角的**创建 Kubernetes 集群**。 

    ![创建集群](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16643/156906241210882_zh-CN.png)

4.  对集群登录方式进行配置，设置通过密钥的登录方式。对集群其他的参数进行配置，参见[ZH-CN\_TP\_16639.md\#](intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes 集群.md#)，最后单击**创建**。 

    1.  若您已在ECS云服务器控制台创建了密钥，只需在密钥对下拉框中进行选择。
    2.  若您没有密钥，单击**新建密钥对**，前往ECS云服务器控制台创建密钥对，参见[创建 SSH 密钥对](../../../../../intl.zh-CN/安全/SSH密钥对/使用SSH密钥对.md#)。
    ![创建密钥对](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16643/156906241210883_zh-CN.png)

5.  集群创建成功后，在集群列表中找到该集群，单击集群右侧的**管理**，在连接信息中查看**Master节点SSH连接地址**。 

    ![连接集群](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16643/156906241210889_zh-CN.png)

6.  下载 .pem私钥文件，按照本地操作系统环境，如 Windows 和 linux 操作系统，进行相关配置，参见[../../../../../dita-oss-bucket/SP\_2/DNA0011894323/ZH-CN\_TP\_9620.md\#](../../../../../intl.zh-CN/实例/连接实例/连接Linux实例/使用SSH密钥对连接Linux实例.md#)。以 linux 环境为例。 
    1.  找到您所下载的.pem私钥文件在本地机上的存储路径，如 `/root/xxx.pem`
    2.  运行命令修改私钥文件的属性：`chmod 400 [.pem私钥文件在本地机上的存储路径]`，如 `chmod 400 /root/xxx.pem`。
    3.  运行命令连接至集群：``ssh -i [.pem私钥文件在本地机上的存储路径] root@[master-public-ip]`，其中 master-public-ip 即是 Master 节点 SSH 连接地址。如 `ssh -i /root/xxx.pem root@10.10.10.100`。

