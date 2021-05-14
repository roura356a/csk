---
keyword: [custom OS image, ACK cluster, quickly add nodes to a cluster, auto scaling]
---

# Custom images

Alicloud Image Builder is an image build tool provided by Alibaba Cloud that simplifies and automates image building. You can use OS images created by using Alicloud Image Builder as custom images to create node pools in Container Service for Kubernetes \(ACK\) clusters. This allows you to quickly add nodes to ACK clusters. This topic describes how to run Alicloud Image Builder as a Job to create custom OS images in ACK clusters.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A kubectl client is connected to the cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

The node pools in ACK clusters support auto scaling. By default, when you create a node pool, you can select OS images such as CentOS and Alibaba Cloud Linux 2. These OS images can meet the requirements of most scenarios. However, in scenarios that require preinstallation or high performance, these images may be unable to meet your requirements. Alibaba Cloud provides Alicloud Image Builder to help you build custom OS images and facilitate auto scaling in complex scenarios.

To use Alicloud Image Builder to create custom images, you can create a Job or a CronJob to distribute the image build task in the cluster.

## Create a Job to quickly build a custom OS image

In this example, a ConfigMap named build-config and a Job named build are created to show how to use Alicloud Image Builder to quickly build a custom OS image.

1.  Create a ConfigMap named build-config to specify the parameters for the OS image.

    1.  Create a YAML file named build-config.yaml and add the following content to the file:

        ```
        apiVersion: v1
        kind: ConfigMap
        metadata:
          name: build-config
        data:
          ack.json: |-
            
            {
              "variables": {
                "region": "{{env `REGION`}}",
                "image_name": "ack-custom_image",
                "source_image": "centos_7_02_64_20G_alibase_20170818.vhd",
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
                  "instance_type": "ecs.g6.large",
                  "skip_image_validation": "true",
                  "io_optimized": "true"
                }
              ],
              "provisioners": [{
                "type": "shell",
                "inline": [
                            "sleep 30",
                ]
              }]
            }
        ```

        The following table describes the parameters in the YAML file.

        |Parameter|Example|Description|
        |---------|-------|-----------|
        |`variables{"<variable1>":"<value>"}`|variables\{"access\_key":"\{\{env ALICLOUD\_ACCESS\_KEY\}\}"\}|The `variables` that are used by Alicloud Image Builder. **Note:** If you write sensitive information such as AccessKey pairs \(`access_key` and `secret_key`\) into the configuration file, such information may be leaked. To mitigate risks, you can specify sensitive information as variables. The values of variables are based on the input values during application execution. |
        |`builders{"type":"<value>"}`|builders\{"type":"alicloud-ecs"\}|The image `builders`. When type is set to aliyun-ecs, a temporary Elastic Compute Service \(ECS\) instance is created to build the image. The ECS instance is automatically released after the image is built.|
        |`provisioners{"type":"<value>"}`|provisioners\{"type":"shell"\}|The image `provisioners` that are used to specify the operations that need to be performed on the temporary instance. When type is set to shell, a shell provisioner is used. A shell command is automatically run after the Linux instance is connected. For example, you can run the `yum install redis.x86_64 -y` command to install Redis. For more information about how to configure provisioners, see [Provisioner configuration](#section_b5b_tsw_zj6). |

        |Parameter|Example|Description|Importance|
        |---------|-------|-----------|----------|
        |access\_key|LTAInPyXXXXQ\*\*\*\*|Your AccessKey ID. For more information, see [Obtain an AccessKey pair]().|High|
        |secret\_key|CM1ycKrrCekQ0dhXXXXXXXXXl7y\*\*\*\*|Your AccessKey secret.|High|
        |region|cn-beijing|The region where the custom image is to be created.|High|
        |image\_name|ack-custom\_image|The name of the custom image to be created. The name must be globally unique.|Low|
        |source\_image|aliyun\_2\_1903\_x64\_20G\_alibase\_20200904.vhd|The ID of the Alibaba Cloud public image based on which to create the custom image. The created custom image will contain the same operating system as the public image.|High|

    2.  Run the following command to deploy Alicloud Image Builder in the cluster:

        ```
        kubectl apply -f build-config.yaml
        ```

2.  Create a Job to build a custom OS image.

    1.  Create a YAML file named build.yaml and add the following content to the file:

        ```
        apiVersion: batch/v1
        kind: Job
        metadata:
          name: image-builder
          namespace: default
        spec:
          template:
            metadata:
              name: image-builder
            spec:
              containers:
                - name: image-builder
                  image: registry.cn-hangzhou.aliyuncs.com/acs/image-builder:v1
                  env:
                    - name: ALICLOUD_ACCESS_KEY  
                      value: xxxxxxx
                    - name: ALICLOUD_SECRET_KEY
                      value: xxxxxxx
                    - name: REGION
                      value: cn-hangzhou
                  command: ["packer"]
                  args:  ["build","/config/ack.json"]
                  volumeMounts:
                    - name: config
                      mountPath: /config
              volumes:
                - name: config
                  configMap:
                    name: build-config
                    items:
                      - key: ack.json
                        path: ack.json
              restartPolicy: Never
        ```

    2.  Run the following command to deploy the Job and start building the image:

        ```
        kubectl apply -f build.yaml
        ```

3.  Log on to the ACK console and check the image build log.

    A log is generated during the image build process. This log records all the image build operations, including checking parameters, creating temporary resources, pre-installing software, creating target resources, and releasing temporary resources. You can check the image build log by performing the following steps:

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Jobs**.

    5.  On the Jobs page, find the Job that you created and click **Details** in the **Actions** column.

    6.  On the job details page, click the **Logs** tab to check the image build log.


## Provisioner configuration

A provisioner is a component used to install and configure software in a running machine before the machine is converted to a static OS image. It is often used to install software in images in the following scenarios:

-   Install software packages.
-   Patch kernels.
-   Create users.
-   Download application code.

Common operations by using provisioners include:

-   Execute shell scripts.

    ```
      "provisioners": [{
          "type": "shell",
          "script": "script.sh"
      }]
    ```

-   Execute orchestration scripts by using Ansible.

    ```
      "provisioners": [
        {
        "type": "ansible",
        "playbook_file": "./playbook.yml"
        }
      ]
    ```

-   Install the Cloud Paralleled File System \(CPFS\) client.

    The installation of CPFS requires multiple installation packages, some of which involves real-time compilation and may require a long time to install. The use of a custom image can greatly reduce the cost of installing the CPFS client on a large number of nodes. The following code block provides a sample configuration.

    ```
        {
          "variables": {
            "region": "{{env `REGION`}}",
            "image_name": "ack-custom_image",
            "source_image": "centos_7_04_64_20G_alibase_201701015.vhd",
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
              "instance_type": "ecs.g6.large",
              "skip_image_validation": "true",
              "io_optimized": "true"
            }
          ],
       "provisioners": [{
            "type": "shell",
            "inline": [
                "cd $HOME",
                "wget https://cpfs-client.oss-cn-beijing.aliyuncs.com/kernel/kernel-devel-`uname -r`.rpm",
                "rpm -ivh --replacefiles kernel-devel-`uname -r`.rpm"
            ]
          }]
    ```


## What to do next

After a custom image is created by using Alicloud Image Builder, you can create an elastic node pool based on the custom image to quickly add nodes to the cluster. For more information about how to create an elastic node pool, see [Auto scaling of nodes](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md).

**Related topics**  


[Create a Kubernetes cluster by using a custom image](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a Kubernetes cluster by using a custom image.md)

