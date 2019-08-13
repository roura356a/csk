# 使用自定义镜像创建Kubernetes集群 {#task_1563283 .task}

随着云原生时代的到来，用户应用、业务上云的需求也越来越多，不同的业务场景对容器平台的需求也不尽相同，其中一个非常重要的需求就是使用自定义镜像创建Kubernetes集群。

容器服务支持用户使用自定义镜像创建Kubernetes集群，但用户在制作打包自定义镜像时，往往会遇到以下情况：

-   人工操作步骤，效率低
-   镜像变更历史记录缺失，不便于故障定位
-   无法对自定义镜像进行校验并判断是否符合Kubernetes集群节点要求

基于以上痛点，我们开源了[ack-image-builder](https://github.com/AliyunContainerService/ack-image-builder)项目帮助用户快速制作符合ACK集群节点要求的自定义镜像。

ack-image-builder 项目基于开源工具[HashiCorp Packer](https://www.packer.io/)，提供默认配置模板和校验脚本。

使用ACK-image-builder项目创建kubernetes集群自定义节点镜像的步骤如下。

1.  安装Packer。 从[官方下载页面](https://www.packer.io/downloads.html)选择操作系统对应的软件版本，并按照[安装说明文档](https://www.packer.io/intro/getting-started/install.html)安装和验证packer。

    执行如下命令，显示结果如下时，说明packer已安装成功。

    ``` {#codeblock_tls_j0s_skn}
    $ packer version
    Packer v1.4.1
    ```

2.  定义Packer模板。 使用Packer创建自定义镜像时，需要创建一个JSON格式的模板文件。在该模板文件中，您需要指定创建自定义镜像的[Alicloud Image Builder（生成器）](https://www.packer.io/docs/builders/alicloud-ecs.html)和[Provisioners（配置器）](https://www.packer.io/docs/provisioners/index.html)。

    ``` {#codeblock_ces_aep_8ru}
    {
      "variables": {
        "region": "cn-hangzhou",
        "image_name": "test_image{{timestamp}}",
        "source_image": "centos_7_06_64_20G_alibase_20190711.vhd",
        "instance_type": "ecs.n1.large",
        "access_key": "{{env `ALICLOUD_ACCESS_KEY`}}",
        "secret_key": "{{env `ALICLOUD_SECRET_KEY`}}"
      },
      "builders": [
        {
          "type": "alicloud-ecs",
          "access_key": "{{user `access_key`}}",
          "secret_key": "{{user `secret_key`}}",
          "region": "{{user `region`}}",
          "image_name": "{{user `image_name`}}",
          "source_image": "{{user `source_image`}}",
          "ssh_username": "root",
          "instance_type": "{{user `instance_type`}}",
          "io_optimized": "true"
        }
      ],
      "provisioners": [
        {
          "type": "shell",
          "scripts": [
            "scripts/updateKernel.sh",
            "scripts/reboot.sh",
            "scripts/cleanUpKerneles.sh",
            "config/default.sh",
            "scripts/updateDNS.sh",
            "scripts/verify.sh"
          ],
          "expect_disconnect": true
        }
      ]
    }
    ```

    |参数|描述|
    |--|--|
    |access\_key|您的AccessKeyID|
    |secret\_key|您的AccessKeySecret|
    |region|创建自定义镜像时使用临时资源的地域|
    |image\_name|自定义镜像的名称|
    |source\_image|基础镜像的名称，可以从阿里云公共镜像列表获得|
    |instance\_type|创建自定义镜像时生成的临时实例的类型|
    |provisioners|创建自定义镜像时使用的 Packer 配置器类型|

3.  创建子账号并生成AccessKey。 制作自定义镜像的权限要求较大，一般建议用户创建子账户并授权Packer需要的对应[RAM Policy](https://github.com/AliyunContainerService/ack-image-builder)并[创建AccessKey](../../../../intl.zh-CN/通用参考/创建AccessKey.md#)。
4.  导入AccessKey信息并制作自定义镜像。 
    1.  执行如下命令，导入AccessKey信息。 

        ``` {#codeblock_5lq_6cr_6vs}
        export ALICLOUD_ACCESS_KEY=XXXXXX
        export ALICLOUD_SECRET_KEY=XXXXXX
        ```

    2.  执行如下命令，制作自定义镜像。 

        ``` {#codeblock_hq0_8f7_i2j}
        $ packer build alicloud.json
        alicloud-ecs output will be in this color.
        
        ==> alicloud-ecs: Prevalidating source region and copied regions...
        ==> alicloud-ecs: Prevalidating image name...
            alicloud-ecs: Found image ID: centos_7_06_64_20G_alibase_20190711.vhd
        ==> alicloud-ecs: Creating temporary keypair: xxxxxx
        ==> alicloud-ecs: Creating vpc...
            alicloud-ecs: Created vpc: xxxxxx
        ==> alicloud-ecs: Creating vswitch...
            alicloud-ecs: Created vswitch: xxxxxx
        ==> alicloud-ecs: Creating security group...
            alicloud-ecs: Created security group: xxxxxx
        ==> alicloud-ecs: Creating instance...
            alicloud-ecs: Created instance: xxxxxx
        ==> alicloud-ecs: Allocating eip...
            alicloud-ecs: Allocated eip: xxxxxx
            alicloud-ecs: Attach keypair xxxxxx to instance: xxxxxx
        ==> alicloud-ecs: Starting instance: xxxxxx
        ==> alicloud-ecs: Using ssh communicator to connect: 47.111.127.54
        ==> alicloud-ecs: Waiting for SSH to become available...
        ==> alicloud-ecs: Connected to SSH!
        ==> alicloud-ecs: Provisioning with shell script: scripts/verify.sh
            alicloud-ecs: [20190726 11:04:10]: Check if kernel version >= 3.10.  Verify Passed!
            alicloud-ecs: [20190726 11:04:10]: Check if systemd version >= 219.  Verify Passed!
            alicloud-ecs: [20190726 11:04:10]: Check if sshd is running and listen on port 22.  Verify Passed!
            alicloud-ecs: [20190726 11:04:10]: Check if cloud-init is installed.  Verify Passed!
            alicloud-ecs: [20190726 11:04:10]: Check if wget is installed.  Verify Passed!
            alicloud-ecs: [20190726 11:04:10]: Check if curl is installed.  Verify Passed!
            alicloud-ecs: [20190726 11:04:10]: Check if kubeadm is cleaned up.  Verify Passed!
            alicloud-ecs: [20190726 11:04:10]: Check if kubelet is cleaned up.  Verify Passed!
            alicloud-ecs: [20190726 11:04:10]: Check if kubectl is cleaned up.  Verify Passed!
            alicloud-ecs: [20190726 11:04:10]: Check if kubernetes-cni is cleaned up.  Verify Passed!
        ==> alicloud-ecs: Stopping instance: xxxxxx
        ==> alicloud-ecs: Waiting instance stopped: xxxxxx
        ==> alicloud-ecs: Creating image: test_image1564110199
            alicloud-ecs: Detach keypair xxxxxx from instance: xxxxxxx
        ==> alicloud-ecs: Cleaning up 'EIP'
        ==> alicloud-ecs: Cleaning up 'instance'
        ==> alicloud-ecs: Cleaning up 'security group'
        ==> alicloud-ecs: Cleaning up 'vSwitch'
        ==> alicloud-ecs: Cleaning up 'VPC'
        ==> alicloud-ecs: Deleting temporary keypair...
        Build 'alicloud-ecs' finished.
        
        ==> Builds finished. The artifacts of successful builds are:
        --> alicloud-ecs: Alicloud images were created:
        
        cn-hangzhou: m-bp1aifbnupnaktj00q7s
        ```

        其中[scripts/verify.sh](https://github.com/AliyunContainerService/ack-image-builder/blob/master/scripts/verify.sh)为对检查项的校验部分。

5.  使用自定义镜像创建Kubernetes集群。 
    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在 Kubernetes 菜单下，单击左侧导航栏的**集群** \> **集群**，进入集群列表页面。
    3.  单击页面右上角的**创建 Kubernetes 集群**，在弹出的选择集群模板中，选择**标准专有集群**页面，单击**创建**。请参见[创建Kubernetes 集群](../../../../intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes 集群.md#)。
    4.  配置集群创建需要的相关基础信息后，单击**显示高级选项**，**自定义镜像**中单击**选择**，选择自定义镜像进行集群创建。 

        ![自定义镜像](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1240299/156567675754436_zh-CN.png)

    5.  配置完成后，单击**创建集群**。

