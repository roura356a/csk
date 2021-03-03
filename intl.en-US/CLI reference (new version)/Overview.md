---
keyword: [ARC, AlibabaCloud Resourcification CLI, Alibaba Cloud CLI tool]
---

# Overview

AlibabaCloud Resourcification CLI \(ACR\) is a new command-line interface \(CLI\) environment that provides multiple CLI tools. This topic describes the CLI tools supported by ARC, ARC options, and how to use ARC.

## Background information

The previous version of Alibaba Cloud CLI does not support structured parameters, causes high expenses, and may not be user friendly. ARC is a new version of Alibaba Cloud CLI and designed for resourcification. It interacts with you when you set parameters in ARC. This improves user experience. For more information, see [alibabacloud-resourcify-cli](https://github.com/aliyun/alibabacloud-resourcify-cli) and [Releases](https://github.com/aliyun/alibabacloud-resourcify-cli/releases).

## Install ARC

You must install ARC before you can use it. Perform the following steps to install ARC:

1.  Install the Node.js environment.
    -   For macOS

        You can use one of the following methods to install the Node.js environment:

        -   Use the source code package.

            Download the [PKG file](https://nodejs.org/en/download/) from the Node.js site.

        -   Use Homebrew.

            Run the `brew install node` command in Homebrew to install the Node.js environment.

    -   For Windows

        Download the [MSI file](https://nodejs.org/en/download/) from the Node.js site.

2.  Run the following command to install ARC:

    ```
    npm i @alicloud/arc -g
    ```


## CLI tools supported by ARC

ARC supports the following CLI tools:

-   arc-tool: It is a resourcification CLI tool that can be used to set and automatically set the required parameters. It is designed as an auxiliary tool. For example, you can use this tool to set an AccessKey pair.

    Command syntax: `arc-tool <command> [subCommand] [options]`

-   arc: It is a resourcification CLI tool that can be used to manage cloud resources. For example, you can use this tool to create virtual private clouds \(VPCs\).

    Command syntac: `arc <product> <resource> <action> [options]`

-   arc-cs: It is used to run subcommands of arc and designed to manage ACK clusters.

    Command syntac: `arc-cs <resource> <action> [options]`

    **Note:** The values in the angle brackets \(`<>`\) are required. The values in the brackets \(`[]`\) are optional.

    The following figure shows the syntax of arc-cs subcommands.

    ![arccs](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6000461161/p210946.png)


## ARC options

ARC options include:

-   `--profile`: specifies the configuration file used by the arc CLI tool.
-   `-i` \| `--interaction`: enables the interactive mode. The system follows the instructions to set resource information and then manage cluster resources.
-   `--region`: specifies the region where the cloud resources are to be managed.

The complete content in ARC is:

```
usage:
    arc  [subcommands]
    arc  [options]

  A resourcification CLI tool that is used to manage cloud resources.

Subcommands:
    cs                                  Container Service for Kubernetes

Options:
  --profile                             [string]            Specify the configuration file to be used
  --region                              [string]            Specify the region
  -i,--interaction                      [boolean]           Specify whether to enable the interactive mode
```

## Use ARC

This section describes how to use ARC. In the following examples, ACK cluster resources are deployed to demonstrate how to use ARC.

1.  Configure ARC.

    You must configure the AccessKey pair and region before you can use ARC.

    -   Method 1: Set the AccessKey pair, region, and language in interactive mode.

        Run the following command to set the preceding information:

        ```
        arc-tool config
        ```

        Output:

        ```
        ? The AccessKey ID.
        access-key-id access_key_id
        ? The AccessKey secret.
        access-key-secret access_key_secret
        ? Select relevant options or end the configuration. region
        ? The region.
        region cn-beijing
        ? Select relevant options or end the configuration. language
        ? The language used by the CLI.
        language en
        ? Specify whether to display and run commands.
        arc-tool config  --access-key-id access_key_id --access-key-secret access_key_secret --region cn-beijing --language en
        ```

    -   Method 2: Run commands to set the AccessKey pair, region, and language.

        Sample code:

        ```
        arc-tool config set access_key_id <value>
        arc-tool config set access_key_secret <value>
        arc-tool config set region ssss <value>
        arc-tool config set language <value>
        ```

2.  Create a cluster.
    -   Method 1: Create a cluster in interactive mode.

        Run the following command to use the `-i` option to enable the interactive mode:

        ```
        arc-cs cluster create-managed -i
        ```

        Output:

        ```
        ? The following options are mutually exclusive. Select only one of them. login-password
        ? Set the SSH logon password. The password must be 8 to 30 characters in length, and must contain at least three of the following types: uppercase letters, lowercase letters, digits, and special characters.
        login-password Hello1234
        ? Specify a name for the cluster. The name can contain letters, digits, and hyphens (-).
        name cluster-by-arc
        Specify the IDs of vSwitches. Specify one to three vSwitch IDs.
        ? Specify the IDs of vSwitches. Specify one to three vSwitch IDs.
        vswitch-ids.0 vsw-bp1910rnb01vtrvfzek5w
        ? Specify whether to add a second vSwitch ID. vswitch-ids No
        Specify the instance type of worker nodes. You can specify multiple instance types.
        ? Specify the instance type of worker nodes. You can specify multiple instance types.
        worker-instance-types.0 ecs.g6a.xlarge
        ? Specify whether to add a second instance type for worker nodes. worker-instance-types Yes
        ? Specify the instance type of worker nodes. You can specify multiple instance types.
        worker-instance-types.1 ecs.g6.xlarge
        ? Specify whether to add a third instance type for worker nodes. worker-instance-types No
        ? Specify the number of worker nodes. Valid values: 0 to 100.
        num-of-nodes 2
        ? Specify the type of system disk of the worker nodes.
        worker-system-disk-category cloud_efficiency
        ? Specify the size of the system disk of a worker node. Unit: GiB.
        worker-system-disk-size 120
        ? Specify the ID of the VPC where the ACK cluster is deployed.
        vpcid vpc-bp1qjw8u2gk7jeipp2n21
        ? Specify the CIDR block of services. This CIDR block cannot overlap with that of the VPC or containers. If the VPC is automatically created by the system, the CIDR block of services is set to 172.19.0.0/20.
        service-cidr 172.19.0.0/20
        ? Select relevant options or end the configuration. security-group-id | is-enterprise
        ? The following options are mutually exclusive. Select only one of them. is-enterprise
        ? Specify whether to create an advanced security group.
        is-enterprise true
        ? Select relevant options or end the configuration. region
        ? Specify the ID of the region where the ACK cluster is deployed.
        region cn-hangzhou
        ? Select relevant options or end the configuration. worker-instance-charge-type
        ? Specify the billing method of the worker nodes.
        PrePaid: subscription.
        PostPaid: pay-as-you-go.
        worker-instance-charge-type PostPaid
        ? Select relevant options or end the configuration. os-type
        ? Specify the type of operating system run by the nodes where the pods are provisioned.
        os-type Linux
        ? Select relevant options or end the configuration. platform
        ? Specify the architecture of the nodes that run pods, for example, x86.
        platform AliyunLinux
        ? Select relevant options or end the configuration. deletion-protection
        ? Specifies whether to enable cluster deletion protection. If this option is enabled, the cluster cannot be deleted in the console or by calling API operations.
        deletion-protection false
        ? Select relevant options or end the configuration. [DONE]
        ? Specify whether to display and run commands.
        arc-cs cluster create-managed  --login-password Hello**** --name cluster-by-arc --vswitch-ids '["vsw-bp1910rnb01vtrvfz****"]' --worker-instance-types '["ecs.g6a.xlarge","ecs.g6.xlarge"]' --num-of-nodes 2 --worker-system-disk-category cloud_efficiency --worker-system-disk-size 120 --vpcid vpc-bp1qjw8u2gk7jeipp**** --service-cidr 172.19.0.0/20 --is-enterprise true --region cn-hangzhou --worker-instance-charge-type PostPaid --os-type Linux --platform AliyunLinux --deletion-protection false
        ```

    -   Method 2: Run commands to create a cluster.

        Sample command:

        ```
        arc-cs cluster create-managed  --login-password Hello**** --worker-instance-types '["ecs.g6a.xlarge"]' --num-of-nodes 2 --worker-system-disk-category cloud_essd --worker-system-disk-size 120 --vpcid vpc-bp1qjw8u2gk7jeipp**** --name test-cli2 --region cn-hangzhou --container-cidr 172.22.0.0/16 --service-cidr 172.19.0.0/20 --vswitch-ids '["vsw-bp1910rnb01vtrvfz****"]'
        ```


