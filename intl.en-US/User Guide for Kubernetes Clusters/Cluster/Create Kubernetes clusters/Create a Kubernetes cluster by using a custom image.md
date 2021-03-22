# Create a Kubernetes cluster by using a custom image

An increasingly large number of users want to migrate their applications and services to the cloud. Different service scenarios have different requirements for the container platforms. One of the common requirements is the ability to create Kubernetes clusters by using custom images. This topic describes how to create a Kubernetes cluster by using a custom image.

Before you create Kubernetes clusters by using custom images, familiarize yourself with the following requirements:

-   We recommend that you use the latest base image supported by Container Service to create a custom image. This base image meets the requirements of deploying Kubernetes clusters and is tested by the container service team. Custom images must meet the following requirements:
    -   Alibaba Cloud cloud-init can be installed. For more information, see [Install cloud-init](/intl.en-US/Images/Custom image/Import images/Install cloud-init.md).
    -   sshd server is enabled and the default port 22 is used.
    -   Time synchronization is performed by using a Network Time Protocol \(NTP\) server provided by Alibaba Cloud.
-   If you want to use custom images, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

Container Service for Kubernetes allows you to create clusters by using custom images. However, you may face the following challenges when you create custom images:

-   It is not efficient to manually create images.
-   The image change history is not well recorded, which makes it difficult to locate faults.
-   You cannot verify whether the custom images meet the requirements of Kubernetes cluster nodes.

To solve the above issues, we launch the open source [ack-image-builder](https://github.com/AliyunContainerService/ack-image-builder) project to help you create custom images that meet the requirements of Kubernetes cluster nodes in an efficient manner.

The ack-image-builder project is developed based on open source tool [HashiCorp Packer](https://www.packer.io/), and provides the default configuration template and verification script for you to create custom images.

To create a custom image by using ack-image-builder, perform the following steps:

1.  Install Packer.

    Download Packer from its [official website](https://www.packer.io/downloads.html). Make sure that the version is compatible with your operating system. Then, install and verify Packer by following [installation instructions](https://www.packer.io/intro/getting-started/install.html).

    Run the packer version command. If the version number of Packer is returned, Packer is installed.

    ```
    packer version
    ```

    ```
    Packer v1.4.1
    ```

2.  Configure a template file.

    If you want to use Packer to create a custom image, you must create a template file in JSON format. In the template file, specify the [Alibaba Cloud image builder](https://www.packer.io/docs/builders/alicloud-ecs.html) and [provisioner](https://www.packer.io/docs/provisioners/index.html) that are used to create the custom image. The following content provides an example.

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

    |Parameter|Description|
    |---------|-----------|
    |access\_key|The AccessKey ID of your account.|
    |secret\_key|The AccessKey secret of your account.|
    |region|The region of the intermediate instance that is used to create the custom image.|
    |image\_name|The name of the custom image.|
    |source\_image|The name of the base image used to create the custom image. Set this parameter to the name of a public image provided by Alibaba Cloud.|
    |instance\_type|The type of the intermediate instance used to create the custom image.|
    |provisioners|The type of the provisioner used to create the custom image.|

3.  Create a Resource Access Management \(RAM\) user and generate an AccessKey pair.

    To meet the permission requirements of creating custom images, we recommend that you create a RAM user, configure a [RAM policy](https://github.com/AliyunContainerService/ack-image-builder) to grant the permissions required by Packer to the RAM user, and [create an AccessKey pair]().

4.  Add the AccessKey pair information to the template and create a custom image.

    1.  Run the following commands to add the AccessKey information to the template:

        ```
        export ALICLOUD_ACCESS_KEY=XXXXXX
        export ALICLOUD_SECRET_KEY=XXXXXX
        ```

    2.  Run the following command to create a custom image:

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

        The content below [scripts/verify.sh](https://github.com/AliyunContainerService/ack-image-builder/blob/master/scripts/verify.sh) indicates the verification results of the custom image.

5.  Use the custom image to create a Kubernetes cluster.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, click **Clusters**.

    3.  In the upper-right corner of the Clusters page, click **Create Kubernetes Cluster**.

    4.  In the Select Cluster Template dialog box, find **Standard Dedicated Cluster** and click **Create**. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).

    5.  On the page that appears, configure the basic parameters and then click **Show Advanced Options**. Click **Select** in the **Custom Image** section.

    6.  On the Choose Custom Image page, click **Use** next to the custom image that you want to use.

    7.  After the configuration is complete, click **Create Cluster**.

        The cluster is created by using the custom image. The custom image can be used in activities such as capacity expansion.


