---
keyword: [自定义镜像, 创建ACK集群]
---

# 使用自定义镜像创建ACK集群

在ACK迁移业务场景中，如无特殊需求，我们推荐您使用ACK默认的系统镜像及其他系统服务。

迁云业务中，推荐您使用ACK默认的系统镜像（CentOS7.6或AliyunLinux 2.1903）及其他系统服务，例如系统内核、DNS、YUM源等。 如有特殊需求需要制作自定义系统镜像，我们开源了[ack-image-builder](https://github.com/AliyunContainerService/ack-image-builder)工具帮助您快速制作符合ACK集群节点要求的自定义镜像。

## 使用ack-image-builder创建自定义镜像

ack-image-builder项目基于开源工具[HashiCorp Packer](https://www.packer.io/)，提供默认配置模板和校验脚本。

使用ack-image-builder可以降低人工操作的错误率，同时也能记录镜像变更历史，便于故障定位。使用ack-image-builder项目创建ACK集群自定义节点镜像的步骤如下：

1.  安装Packer。

    从[官方下载页面](https://www.packer.io/downloads.html)选择操作系统对应的软件版本，并按照[安装说明文档](https://www.packer.io/intro/getting-started/install.html)安装和验证 Packer。

    执行以下命令，显示结果如下时，说明Packer已安装成功。

    ```
    packer version
    ```

    ```
    Packer v1.4.1
    ```

2.  定义Packer 模板。

    使用Packer创建自定义镜像时，需要创建一个JSON格式的模板文件。在该模板文件中，您需要指定创建自定义镜像的[Alicloud Image Builder（生成器）](https://www.packer.io/docs/builders/alicloud-ecs.html)和[Provisioners（配置器）](https://www.packer.io/docs/provisioners/index.html)。

    ```
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
            "config/default.sh",
            "scripts/updateDNS.sh",
            "scripts/reboot.sh",
            "scripts/verify.sh"
          ],
          "expect_disconnect": true
        }
      ]
    }
    ```

    |参数|描述|
    |--|--|
    |access\_key|您的AccessKeyID。|
    |secret\_key|您的AccessKeySecret。|
    |region|创建自定义镜像时使用临时资源的地域。|
    |image\_name|自定义镜像的名称。|
    |source\_image|基础镜像的名称，可以从阿里云公共镜像列表获得。|
    |instance\_type|创建自定义镜像时生成的临时实例的类型。|
    |provisioners|创建自定义镜像时使用的 Packer 配置器类型。|

3.  创建子账号并生成AccessKey。

    制作自定义镜像的权限要求较大，建议您创建子账户并授权Packer需要的对应 [RAM Policy](https://github.com/AliyunContainerService/ack-image-builder) 并[创建AccessKey]()。

4.  导入AccessKey信息并制作自定义镜像。

    1.  执行以下命令，导入AccessKey信息。

        ```
        export ALICLOUD_ACCESS_KEY=XXXXXX
        export ALICLOUD_SECRET_KEY=XXXXXX
        ```

    2.  执行以下命令，制作自定义镜像。

        ```
        packer build alicloud.json
        ```

        ```
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

        其中 [scripts/verify.sh](https://github.com/AliyunContainerService/ack-image-builder/blob/master/scripts/verify.sh) 为对检查项的校验部分。


## 使用自定义操作系统内核

ACK要求自定义操作系统内核版本不小于`3.10`，请仅更新您需要自定义安装的相关rpm包，并设置正确的内核引导项。

示例如下。

```
cat scripts/updateOSKernel.sh
#!/bin/bash
VERSION_KERNEL="3.10.0-1062.4.3.el7"
yum  localinstall -y  http://xxx.xxx.xxx.xxx/kernel-${VERSION_KERNEL}.x86_64.rpm   http://xxx.xxx.xxx.xxx/kernel-devel-${VERSION_KERNEL}.x86_64.rpm   http://xxx.xxx.xxx.xxx/kernel-headers-${VERSION_KERNEL}.x86_64.rpm
grub_num=$(cat /etc/grub2.cfg |awk -F\' '$1=="menuentry " {print i++ " : " $2}' |grep $VERSION_KERNEL |awk -F ':' '{print $1}')
grub2-set-default $grub_num
```

**说明：** 不推荐使用`yum update -y`等全局更新的命令。

## 使用自定义内核参数

如果您需设置自定义内核参数，请注意不要覆盖以下参数项。

```
["vm.max_map_count"]="262144"
["kernel.softlockup_panic"]="1"
["kernel.softlockup_all_cpu_backtrace"]="1"
["net.core.somaxconn"]="32768"
["net.core.rmem_max"]="16777216"
["net.core.wmem_max"]="16777216"
["net.ipv4.tcp_wmem"]="4096 12582912 16777216"
["net.ipv4.tcp_rmem"]="4096 12582912 16777216"
["net.ipv4.tcp_max_syn_backlog"]="8096"
["net.ipv4.tcp_slow_start_after_idle"]="0"
["net.core.netdev_max_backlog"]="16384"
["fs.file-max"]="2097152"
["fs.inotify.max_user_instances"]="8192"
["fs.inotify.max_user_watches"]="524288"
["fs.inotify.max_queued_events"]="16384"
["net.ipv4.ip_forward"]="1"
["net.bridge.bridge-nf-call-iptables"]="1"
["fs.may_detach_mounts"]="1"
["net.ipv4.conf.default.rp_filter"]="0"
["net.ipv4.tcp_tw_reuse"]="0"
["net.ipv4.tcp_tw_recycle"]="0"
```

**说明：** 如若以上某些参数必须覆盖，请先申请工单请求ACK的同学评估影响。然后在控制台创建集群或扩容集群页面的**高级选项** \> **实例自定义数据**处填写对应的脚本。

## 使用自定义DNS服务

使用自定义DNS服务需要关注以下几点：

-   自定义DNS服务的upstream nameserver必须添加阿里云的nameserver 。

    ```
    cat /etc/resolv.conf
    options timeout:2 attempts:3 rotate single-request-reopen
    ; generated by /usr/sbin/dhclient-script
    nameserver 100.XX.XX.136
    nameserver 100.XX.XX.138
    ```

-   修改/etc/resolve.conf后必须锁定文件，不然ECS实例重启后cloud-init会刷新文件恢复默认设置，示例如下：

    ```
    cat scripts/updateDNS.sh
    #!/bin/bash
    # unlock DNS file in case it was locked
    chattr -i /etc/resolv.conf
    
    # Using your custom nameserver to replace xxx.xxx.xxx.xxx
    echo -e "nameserver xxx.xxx.xxx.xxx\nnameserver xxx.xxx.xxx.xxx" > /etc/resolv.conf
    
    # Keep resolv locked to prevent overwriting by cloudinit/NetworkManager
    chattr +i /etc/resolv.conf
    ```

-   确保自定义DNS服务的性能。

    如果ACK容器集群规模较大，请确保自定义DNS服务的性能能够满足您的业务需求。


## 使用自定义YUM源

使用自定义YUM源需要关注以下两点：

-   避免RPM包的全量更新 。

    只更新需要安装部署的rpm包，避免使用`yum update -y`命令。

-   自定义YUM源的性能 。

    如果ACK集群单次扩容规模较大且依赖YUM源的话，请确保YUM源的性能能够满足您的业务需求， 示例如下。

    ```
    cat scripts/add-yum-repo.sh
    #!/bin/bash
    cat << EOF > /etc/yum.repos.d/my.repo
    [base]
    name=CentOS-\$releasever
    enabled=1
    failovermethod=priority
    baseurl=http://mirrors.cloud.aliyuncs.com/centos/\$releasever/os/\$basearch/
    gpgcheck=1
    gpgkey=http://mirrors.cloud.aliyuncs.com/centos/RPM-GPG-KEY-CentOS-7
    EOF
    ```


## ACK系统组件容器镜像预加载

当ACK集群单次扩容规模大于1000台时，推荐您在制作自定义系统镜像时预加载ACK的一些DaemonSet资源类型的系统组件容器镜像，这样可以减少节点启动时拉取容器镜像的操作，优化扩容效率。

1.  将相关系统组件容器镜像打tar包存储在镜像中。

    本例中，假设您创建的集群使用Terway网络、CSI存储插件，镜像用于cn-hangzhou地域的集群，则预加载脚本示例如下：

    ```
    cat scripts/prepare-images.sh
    #!/bin/bash
    set -x -e
    EXPORT_PATH=/preheated
    
    # 安装 Docker
    yum install -y docker
    systemctl start docker
    
    # 批量拉取、保存镜像
    images=(
    registry-vpc.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.5.60-5318afe-aliyun
    registry-vpc.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.78-g97729ee-aliyun
    )
    
    mkdir -p ${EXPORT_PATH}
    
    for image in "${images[@]}"; do
        echo "preheating ${image}"
        docker pull ${image}
        docker save -o ${EXPORT_PATH}/$(echo ${image}| md5sum | cut -f1 -d" ").tar ${image}
    done
    
    # 卸载 Docker
    yum erase -y docker
    rm -rf /var/lib/docker
    ```

2.  登录[容器服务管理控制台](https://cs.console.aliyun.com)，在创建集群的**高级选项** \> **实例自定义数据**处填写以下脚本。

    ```
    ls /preheated/ | xargs -n 1 -i docker load -i /preheated/{}
    rm -rf /preheated
    ```


## 自定义镜像的配置文件

在`alicloud.json`文件中的`provisioners`添加以下内容，生成自定义系统镜像。

```
  "provisioners": [
    {
      "type": "shell",
      "scripts": [
        "config/default.sh",
        "scripts/updateOSKernel.sh",
        "scripts/updateDNS.sh",
        "scripts/add-yum-repo.sh",
        "scripts/prepare-images.sh",
        "scripts/reboot.sh",
        "scripts/verify.sh"
      ],
      "expect_disconnect": true
    }
  ]
```

**说明：** `config/default.sh`、`scripts/reboot.sh`和`scripts/verify.sh`为默认必须执行的脚本，其他为用户自定义的脚本。

`config/default.sh`中设置时区并关闭swap分区。

`scripts/verify.sh`中检查自定义镜像是否符合ACK集群节点要求。

此时，您可以使用自定义系统镜像扩容ACK集群并进行验证。

## 创建ACK集群

为了节约创建集群的时间，以及降低出错概率，您可以先创建一个无Worker节点的专有版集群或者选择2个Worker节点的托管版集群，然后选择自定义镜像进行集群扩容 ，并进行验证。

1.  创建一个基于ACK默认系统镜像的3Masters+0Worker或者5Masters+0Worker的集群。请参见[创建Kubernetes专有版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。

    **说明：** 如果您创建的是托管版Kubernetes集群，需要至少选择2个Worker节点。请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

2.  选择自定义镜像进行集群扩容，请参见[扩容集群](/cn.zh-CN/Kubernetes集群用户指南/集群/扩容集群.md)。

    如若需要在自定义镜像节点添加到ACK集群后运行其他初始化脚本，可以选择填写实例自定义数据。

    **说明：** 您可以通过[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)申请开通**自定义镜像选择**和**实例自定义数据**功能。


