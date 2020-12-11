# Synchronize images from a user-created Harbor instance to Container Registry Default Instance Edition

Container Registry is a service that is provided by Alibaba Cloud to manage container images. It supports lifecycle management of container images in 20 regions around the globe. Container Registry is integrated with other Alibaba Cloud services such as Container Service for Kubernetes to provide an all-in-one solution to manage cloud-native applications. This topic describes how to use image-syncer to synchronize images from a user-created Harbor instance to Container Registry Default Instance Edition.

The Container Registry service is activated.

Log on to the [Container Registry console](https//cr.console.aliyun.com) and activate the Container Registry service.

## Create a namespace

A namespace allows you to manage a collection of repositories. You can manage permissions on repositories and repository attributes. You can enable **Automatically Create Repository** for a namespace. When you run the docker push command to push images to a repository that does not exist in the namespace, the repository is automatically created.

**Note:** The repository that is created by using the docker push command can be public or private based on the default repository type of the namespace.

1.  Log on to the [Container Registry console](https//cr.console.aliyun.com).

2.  In the left-side navigation pane, choose **Default Instance** \> **Namespaces**.

3.  On the Namespaces page, click **Create Namespace** in the upper-right corner.

4.  In the Create Namespace dialog box, customize a namespace and click **Confirm**.


After the namespace is created, you can find it on the Namespaces page. You can also manage namespaces on the Namespaces page. For more information, see [Basic operations on a namespace]().

## Grant permissions to a RAM user

Before you perform operations as a Resource Access Management \(RAM\) user, create a RAM user and grant permissions to the RAM user. Skip this step if you use an Alibaba Cloud account to perform subsequent operations.

1.  Create a RAM user. For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Create a RAM user.md).

2.  Grant permissions to the RAM user. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create custom RAM permission policies.md).

    In this example, the RAM user requires only permissions to create and update image repositories in the image-syncer namespace. The following policy grants the RAM user the minimum required permissions:

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


## Configure an access credential

Before you pull private images or upload images, you must run the `docker login` command and enter your access credential to log on to the instance. Perform the following steps to configure an access credential:

1.  In the left-side navigation pane, choose **Default Instance** \> **Access Credential**.

2.  On the Access Credential page, click **Set Password**.

3.  In the Set Password dialog box, set **Password** and **Confirm Password** and click **OK**.


You can call an API operation to obtain a temporary token that you can use to access a Container Registry instance. For more information, see [GetAuthorizationToken]().

## Configure image-syncer

In this example, images in the library/nginx repository of a user-created Harbor instance are synchronized to the image-syncer namespace of a Container Registry instance in the China \(Beijing\) region. The destination repository uses the same name as the source repository. The following example shows how to configure image-syncer:

```
{
    "auth": {
        "harbor.myk8s.paas.com:32080": {
            "username": "admin",
            "password": "xxxxxxxxx",
            "insecure": true
        },
        "registry.cn-beijing.aliyuncs.com": {
            "username": "acr_pusher@1938562138124787",
            "password": "xxxxxxxx"
        }
    },
    "images": {
        "harbor.myk8s.paas.com:32080/library/nginx": ""
    }
}
```

-   `harbor.myk8s.paas.com:32080`: the endpoint of the user-created Harbor instance. You must replace the value with an actual endpoint.
    -   `username`: the username of the user-created Harbor instance. The value is admin in this example.
    -   `password`: the password of the user-created Harbor instance.
    -   `insecure`: Set this parameter to true.
-   `registry.cn-beijing.aliyuncs.com`: the endpoint of the destination repository. In this example, the image resides in the China \(Beijing\) region.
    -   `username`: the username in the access credential.
    -   `password`: the password in the access credential.
-   `"harbor.myk8s.paas.com:32080/library/nginx": ""`: Use the endpoint harbor.myk8s.paas.com:32080 to access the library/nginx repository.

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

Each time you synchronize an image, image-syncer generates a synchronization task, runs the task, and retries if the task fails. Each task synchronizes an image. An image is represented by a tag. If a tag is not specified for a rule in the configuration file, image-syncer lists all the tags in the source repository and generates synchronization tasks for all the images. If image-syncer fails to generate synchronization tasks, image-syncer retries after it runs generated tasks.

-   The following figure shows the output of a successful synchronization task.

    ![Success](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7446858951/p71380.png)

-   The following figure shows the output of a failed synchronization task. Possible reasons include invalid usernames or passwords.

    ![Failure](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8446858951/p71384.png)

-   The following figure shows the logs of image-syncer.

    ![Logs](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8446858951/p71386.png)


