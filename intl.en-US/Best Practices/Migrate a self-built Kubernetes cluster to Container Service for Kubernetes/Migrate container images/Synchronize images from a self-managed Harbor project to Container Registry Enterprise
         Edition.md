# Synchronize images from a self-managed Harbor project to Container Registry Enterprise Edition

This topic describes how to use the image-syncer tool to synchronize images from a self-managed Harbor project to an instance of Container Registry Enterprise Edition.

-   Container Registry is activated.

    Log on to the [Container Registry console](https//cr.console.aliyun.com) and activate Container Registry.

-   An instance of Container Registry Enterprise Edition is created. For more information, see [Create a Container Registry Enterprise Edition instance]().

Container Registry Enterprise Edition provides an enterprise-class secure service for managing container images and Helm charts. It provides enterprise-class security and allows you to distribute images to thousands of nodes concurrently and synchronize images among regions on a global scale. It also allows you to create cloud-native application delivery chains to automatically deliver images globally upon source code changes in multiple scenarios. This service applies to enterprise customers that have high security requirements, deploy services in multiple regions, and use clusters that consist of a large number of nodes.

## Step 1: Create namespaces

A namespace is a collection of repositories, including repository permissions and repository properties. You can use namespaces to manage repositories efficiently. You can enable **Automatically Create Repository** for a namespace. When you run the docker push command to push images to a repository that does not exist in the namespace, the repository is automatically created.

1.  Log on to the [Container Registry console](https//cr.console.aliyun.com).

2.  In the left-side navigation pane, choose **Enterprise Instances** \> **Instances**.

3.  Find the created instance and click **Manage** in the Actions column.

4.  In the left-side navigation pane of the details page, choose **Repositories** \> **Namespaces**.

5.  In the upper-right corner of the Namespaces page, click **Create Namespace**.

6.  On the Create Namespace page, select **Automatically Create Repository** and **Default Repository Type**, and click **Confirm**.

    **Note:** You must turn on **Automatically Create Repository**.


After the namespace is created, you can find the created namespace on the Namespaces page. You can also manage namespaces on the Namespaces page. For more information, see [Basic operations on a namespace]().

## Step 2: Authorize a RAM user

If you want to perform the subsequent operations as a Resource Access Management \(RAM\) user, you must create and grant permissions to a RAM user. Skip this step if you want to use an Alibaba Cloud account to perform the subsequent operations.

1.  Create a RAM user. For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Create a RAM user.md).

2.  Grant required permissions to the RAM user. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create custom RAM permission policies.md).

    In this example, you need to grant the RAM user only the permissions to create and update the Container Registry repository. The following code block is used to grant the preceding permissions. The RAM user is allowed to access resources only in the image-syncer namespace.

    ```
    {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "cr:CreateRepository",
                    "cr:UpdateRepository",
                    "cr:PushRepository",
                    "cr:PullRepository"
                ],
                "Resource": [
                    "acs:cr:*:*:repository/image-syncer/*"
                ]
            }
        ],
        "Version": "1"
    }
    ```


## Step 3: Enable Internet access

By default, instances of Container Registry Enterprise Edition cannot be accessed over the Internet. Therefore, you must enable Internet access before you configure the access control policy for Internet access.

1.  Log on to the [Container Registry console](https//cr.console.aliyun.com). In the top menu bar, select the region where the instance is deployed.

2.  In the left-side navigation pane, choose **Enterprise Instances** \> **Instances**.

3.  On the **Instances** page, click the instance of Container Registry Enterprise Edition to be configured.

4.  In the left-side navigation pane, choose **Repositories** \> **Access Control**.

    **Note:** If you want to configure access control for Helm charts, choose **Helm Chart** \> **Access Control**.

5.  On the **Internet** tab, click **Add Internet Whitelist**.

6.  In the **Add Internet Whitelist** dialog box, enter the CIDR block that is allowed to access the instance of Container Registry Enterprise Edition and the description.

7.  Click **OK**.

    After the CIDR block is added, Elastic Compute Service \(ECS\) instances that fall within the CIDR block can access the instance of Container Registry Enterprise Edition.

    **Note:** If you want to allow all ECS instances to access the instance of Container Registry Enterprise Edition over the Internet, you can enable Internet access and delete all whitelists for Internet access. After you perform this task, the instance of Container Registry Enterprise Edition is completely exposed to the Internet and may be attacked. Proceed with caution.


## Step 4: Configure a credential

Before you pull private images or upload images, you must run the `docker login` command to log on to the image registry with a credential. Perform the following steps to configure a credential:

1.  In the left-side navigation pane, choose **Enterprise Instances** \> **Instances**.

2.  On the **Instances** page, click the instance of Container Registry Enterprise Edition to be configured.

3.  In the left-side navigation pane, choose **Default Instance** \> **Access Credential**.

4.  Click **Set Password**.

5.  In the Set Password dialog box, **enter a password** and **enter the password again**, and click **OK**.


You can call an API operation to obtain a temporary token for accessing the instance. For more information, see [GetAuthorizationToken]().

## Step 5: Use image-syncer to synchronize images from the self-managed Harbor project to Container Registry Enterprise Edition

Use the password in the credential to configure image-syncer.

Unlike default instances, each instance of Container Registry Enterprise Edition has its own endpoints and namespaces. Namespaces on different instances of Container Registry Enterprise Edition are isolated from each other.

**Note:** Each instance of Container Registry Enterprise Edition has two endpoints: one for Internet access and the other for virtual private cloud \(VPC\) access.

-   If image-syncer runs on a server on the Internet, you must use the endpoint for Internet access to access the instance of Container Registry Enterprise Edition.
-   If image-syncer runs on an ECS instance in a VPC, you must use the endpoint for VPC access to access the instance of Container Registry Enterprise Edition. In addition, you must make the endpoint visible to the VPC where the ECS instance is deployed.

In this example, images in the library/nginx repository of a self-managed Harbor project are synchronized to the image-syncer namespace on an instance of Container Registry Enterprise Edition. The name of the source repository, which is nginx, is used as the name of the destination repository. The following code block is a sample configuration file:

```
{
    "auth": {
        "harbor.myk8s.paas.com:32080": {
            "username": "admin",
            "password": "xxxxxxxxx",
            "insecure": true
        },
        "ruohe-test-registry.cn-shanghai.cr.aliyuncs.com": {
            "username": "ruohehhy",
            "password": "xxxxxxxx"
        }
    },
    "images": {
        "harbor.myk8s.paas.com:32080/library/nginx": ""
    }
}
```

-   `harbor.myk8s.paas.com:32080`: the endpoint of the self-managed Harbor project. It must be replaced with the actual value.
    -   `username`: the username of the self-managed Harbor project. The value is admin in this example.
    -   `password`: the password of the self-managed Harbor project.
    -   `insecure`: Set this parameter to true.
-   `ruohe-test-registry.cn-shanghai.cr.aliyuncs.com`: the endpoint of the instance of Container Registry Enterprise Edition for Internet access.
    -   `username`: the username in the credential.
    -   `password`: the password in the credential.
-   `"harbor.myk8s.paas.com:32080/library/nginx": ""`: Access the library/nginx repository by using harbor.myk8s.paas.com:32080.

## Use image-syncer to synchronize images

1.  Download the latest installation package of [image-syncer](https://github.com/AliyunContainerService/image-syncer/releases/tag/v1.0.3).

    **Note:** Only the Linux AMD64 version is supported.

2.  Install and configure image-syncer.

    For more information, see [Install and configure image-syncer](https://github.com/AliyunContainerService/image-syncer?spm=a2c6h.12873639.0.0.66b165a8HrkbnA#compile-manually).

3.  Run the following command to synchronize images:

    ```
    # Set the default destination repository to registry.cn-beijing.aliyuncs.com and the default destination namespace to image-syncer.
    # Set both the number of images that can be synchronized at a time and the maximum number of retries to 10.
    # Record logs in the ./log file. If the file does not exist, it is automatically created. By default, image-syncer logs appear in Stderr if the log file is not specified.
    # Specify harbor-to-acr.json as the configuration file. Its content is described in the previous section.
    ./image-syncer --proc=10 --config=./harbor-to-acr.json --registry=registry.cn-beijing.aliyuncs.com --namespace=image-syncer --retries=10 --log=./log
    ```


## Synchronization result

To synchronize images, image-syncer generates a synchronization task, runs the task, and retries failed tasks. Each task synchronizes an image. An image is represented by a tag. If a rule in the configuration file does not specify a tag, image-syncer lists all the tags in the source repository and generates synchronization tasks for all the images. If image-syncer fails to generate synchronization tasks, it retries after it runs generated tasks.

-   The following figure shows the output of a successful synchronization task.

    ![Success](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7446858951/p71380.png)

-   The following figure shows the output of a failed synchronization task. Possible reasons include invalid usernames or passwords.

    ![Failure](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8446858951/p71384.png)

-   The following figure shows the logs of image-syncer.

    ![Logs](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8446858951/p71386.png)


