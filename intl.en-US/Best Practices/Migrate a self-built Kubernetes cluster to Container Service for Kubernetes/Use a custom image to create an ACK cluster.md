# Use a custom image to create an ACK cluster

If you migrate a user-created Kubernetes cluster to an ACK cluster, we recommend that you use the default system image and default system services to create the ACK cluster. However, you can also use a custom image to create ACK clusters based on your business requirements. This topic describes how to use a custom image to create an ACK cluster.

If you migrate a user-created Kubernetes cluster to an ACK cluster, we recommend that you use the default system image for CentOS 7.6 or Alibaba Cloud Linux 2.1903 and default system services. The default system services include the operating system kernel, domain name system \(DNS\) service, and YUM repositories. If you want to use a custom image to create an ACK cluster, use the [ack-image-builder](https://github.com/AliyunContainerService/ack-image-builder) tool to create a custom image.

## Use ack-image-builder to create a custom image

The ack-image-builder tool is developed based on open source tool [HashiCorp Packer](https://www.packer.io/). The ack-image-builder tool provides a default template and a verification script for you to create custom images.

By using ack-image-builder, you can reduce errors caused by manual operations. The ack-image-builder tool also records image changes to facilitate troubleshooting. To use the ack-image-builder tool to create a custom image for an ACK cluster, perform the following steps:

1.  Install Packer.

    Download Packer from its [official website](https://www.packer.io/downloads.html). Make sure that the version is compatible with your operating system. Then, install and verify Packer based on its [installation documentation](https://www.packer.io/intro/getting-started/install.html).

    Run the packer version command. The following command output indicates that Packer is installed.

    ```
    packer version
    ```

    ```
    Packer v1.4.1
    ```

2.  Create a template in Packer.

    Before you use Packer to create a custom image, create a template in JSON format. In the template, you must specify the [image builder](https://www.packer.io/docs/builders/alicloud-ecs.html) and [provisioner](https://www.packer.io/docs/provisioners/index.html) that are used to create a custom image. In this example, Alicloud Image Builder and the shell provisioner are used.

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

    |Parameter|Description|
    |---------|-----------|
    |access\_key|The AccessKey ID of your account.|
    |secret\_key|The AccessKey secret of your account.|
    |region|The region of the temporary instance used to create the custom image.|
    |image\_name|The name of the custom image.|
    |source\_image|The name of the source image that is used to create the custom image. You can obtain the name from the public image list of Alibaba Cloud.|
    |instance\_type|The type of the temporary instance used to create the custom image.|
    |provisioners|The provisioner used to create the custom image.|

3.  Create a Resource Access Management \(RAM\) user and generate an AccessKey pair.

    A wide range of permissions is required to create a custom image. We recommend that you create a RAM user and use a [RAM policy](https://github.com/AliyunContainerService/ack-image-builder) to grant the permissions required by Packer to the RAM user. Then, create an AccessKey pair for the RAM user. For more information, see [Create an AccessKey]().

4.  Add the AccessKey pair information to the template and create a custom image.

    1.  Run the following commands to add the AccessKey pair information:

        ```
        export ALICLOUD_ACCESS_KEY=XXXXXX
        export ALICLOUD_SECRET_KEY=XXXXXX
        ```

    2.  Run the following commands to create a custom image:

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

        The [scripts/verify.sh](https://github.com/AliyunContainerService/ack-image-builder/blob/master/scripts/verify.sh) script is used to verify the custom image.


## Use a custom operating system kernel

ACK requires a Linux operating system with the kernel of `V3.10` or later. We recommend that you update only the RPM packages to be customized. You must set boot parameters for the kernel.

You can use the following code:

```
cat scripts/updateOSKernel.sh
#! /bin/bash
VERSION_KERNEL="3.10.0-1062.4.3.el7"
yum  localinstall -y  http://xxx.xxx.xxx.xxx/kernel-${VERSION_KERNEL}.x86_64.rpm   http://xxx.xxx.xxx.xxx/kernel-devel-${VERSION_KERNEL}.x86_64.rpm   http://xxx.xxx.xxx.xxx/kernel-headers-${VERSION_KERNEL}.x86_64.rpm
grub_num=$(cat /etc/grub2.cfg |awk -F\' '$1=="menuentry " {print i   " : " $2}' |grep $VERSION_KERNEL |awk -F ':' '{print $1}')
grub2-set-default $grub_num
```

**Note:** We recommend that you do not run commands that update all RPM packages, such as the `yum update -y` command.

## Customize the operating system kernel

When you customize kernel parameters, do not overwrite the following parameters:

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

**Note:** If you need to modify some of the preceding parameters, submit a ticket to request Alibaba Cloud engineers to analyze the effects. After you are authorized to modify the preceding parameters, you can go to the page for creating or scaling out a cluster, click **Show Advanced Options**, and then enter your script in the **User Data** field.

## Use a custom DNS service

If you use a custom DNS service, pay attention to the following notes:

-   Add Alibaba Cloud name servers to the upstream name servers of the custom DNS service.

    ```
    cat /etc/resolv.conf
    options timeout:2 attempts:3 rotate single-request-reopen
    ; generated by /usr/sbin/dhclient-script
    nameserver 100.XX.XX.136
    nameserver 100.XX.XX.138
    ```

-   Lock the /etc/resolve.conf file after you modify it. Otherwise, cloud-init restores the file to default settings after ECS instances restart. You can use the following code:

    ```
    cat scripts/updateDNS.sh
    #! /bin/bash
    # unlock DNS file in case it was locked
    chattr -i /etc/resolv.conf
    
    # Using your custom nameserver to replace xxx.xxx.xxx.xxx
    echo -e "nameserver xxx.xxx.xxx.xxx\nnameserver xxx.xxx.xxx.xxx" > /etc/resolv.conf
    
    # Keep resolv locked to prevent overwriting by cloudinit/NetworkManager
    chattr  i /etc/resolv.conf
    ```

-   Ensure adequate performance of the custom DNS service.

    Make sure that the performance of the custom DNS service can meet the requirements if your cluster contains a large number of nodes.


## Use a custom YUM repository

If you use a custom YUM repository, pay attention to the following notes:

-   Do not update all RPM packages.

    Update only the RPM packages to be installed. Do not run the `yum update -y` command to update all RPM packages.

-   Ensure adequate performance of the YUM repository.

    If you want to add a large number of worker nodes to the cluster at a time and update RPM packages based on the YUM repository, make sure that the performance of the YUM repository can meet your business requirements. You can use the following code:

    ```
    cat scripts/add-yum-repo.sh
    #! /bin/bash
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


## Preload the container images of DaemonSet components

If you want to add more than 1,000 worker nodes to the cluster at a time, we recommend that you preload the container images of DaemonSet components before you create the custom image. This reduces the workload of pulling these container images when nodes start and improves the efficiency of cluster scale-outs.

1.  Compress the required container images to TAR packages and store them in the custom image.

    Assume that the ACK cluster uses the Terway network plug-in and the Container Storage Interface \(CSI\) storage plug-in, and resides in the China \(Hangzhou\) region. You can use the following script to preload the container images of the preceding plug-ins:

    ```
    cat scripts/prepare-images.sh
    #! /bin/bash
    set -x -e
    EXPORT_PATH=/preheated
    
    # Install Docker.
    yum install -y docker
    systemctl start docker
    
    # Pull and save images.
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
    
    # Uninstall Docker.
    yum erase -y docker
    rm -rf /var/lib/docker
    ```

2.  Log on to the [ACK console](https://cs.console.aliyun.com). Go to the cluster creation page, click **Show Advanced Options**, and then enter the following script in the **User Data** field:

    ```
    ls /preheated/ | xargs -n 1 -i docker load -i /preheated/{}
    rm -rf /preheated
    ```


## Edit the configuration file of the custom image

Add the following configurations about `provisioners` to the `alicloud.json` file for creating the custom image:

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

**Note:** The `config/default.sh`, `scripts/reboot.sh`, and `scripts/verify.sh` scripts are default scripts that you must run. Others are custom scripts.

The `config/default.sh` script sets the time zone and disables swap partitions.

The `scripts/verify.sh` script checks whether the custom image meets the requirements of the desired ACK cluster.

After you edit the configuration file of the custom image, you can create the custom image and use it to create or scale out an ACK cluster.

## Create an ACK cluster

We recommend that you first create a dedicated Kubernetes cluster that contains no worker nodes or a managed Kubernetes cluster that contains two worker nodes, add worker nodes that use a custom image to the cluster, and verify the result. This saves time and decreases the probability of errors.

1.  Use the default system image to create a dedicated Kubernetes cluster that contains three or five master nodes and no worker nodes. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).

    **Note:** If you create a managed Kubernetes cluster, select at least two worker nodes. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).

2.  Add worker nodes that use a custom image to the cluster. For more information, see [Expand a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Expand a cluster.md).

    If you want to run an initialization script after you add the worker nodes to the cluster, you can configure the user data for Elastic Compute Service \(ECS\) instances.

    **Note:** To use **custom images** and configure the **user data**, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).


