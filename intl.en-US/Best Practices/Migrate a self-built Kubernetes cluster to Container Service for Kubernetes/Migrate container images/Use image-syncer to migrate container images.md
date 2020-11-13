# Use image-syncer to migrate container images

Image migration and synchronization between image repositories are required to migrate applications from a user-created Kubernetes cluster to an ACK cluster. You can use image-syncer to migrate and synchronize multiple images from user-created image repositories to Alibaba Cloud Container Registry \(ACR\) on the fly at a time. This topic describes how to use image-syncer to migrate container images.

Compared with the Kubernetes clusters created and maintained by other cloud providers, Alibaba Cloud Container Service for Kubernetes \(ACK\) is superior in terms of the service cost, maintenance cost, ease-of-use, and long-term stability. Many cloud providers want to migrate their Kubernetes workloads to ACK. If the number of images is small, cloud providers can run the docker pull and docker push commands to migrate the images. If you run the commands to migrate more than a hundred images or an image repository that stores TB-level data, the migration process takes a long time and may cause data loss. In this case, the image synchronization capability is required for migrating images between image repositories. The open source tool image-syncer developed by Alibaba Cloud provides this capability and has helped many cloud vendors migrate images, among which the maximum image repository capacity is larger than 3 TB. The server that runs image-syncer can make full use of the server bandwidth, and no requirement exists for the disk capacity of the server.

## image-syncer overview

Image migration and synchronization between image repositories are required to migrate applications from a user-created Kubernetes cluster to an ACK cluster. The traditional image synchronization method uses a script that contains the docker pull or docker push command and has the following limits:

-   You must remove existing images from the disks of the server where the destination image repository resides and store the migrated images on these disks. Therefore, this method does not apply to large-scale image migration.
-   The Docker daemon is required. The Docker daemon limits the number of images that can be pulled or pushed concurrently. As a result, you cannot perform high-concurrency image synchronization.
-   HTTP APIs are required to implement some features. You cannot synchronize images by using only the Docker CLI. As a result, you must write a complex synchronization script.

image-syncer is an easy-to-use tool for migrating or synchronizing a large number of images at a time. By using image-syncer, you can synchronize Docker images from or to almost all major image repository services based on Docker Registry V2, such as ACR, Docker Hub, Quay.io, and Harbor. This tool has been used to migrate TB-level images in production environments. For more information, see [image-syncer](https://github.com/AliyunContainerService/image-syncer?spm=a2c6h.12873639.0.0.66b165a8HrkbnA).

## Features

image-syncer has the following features:

-   Synchronizes images from multiple source image repositories to multiple destination image repositories.
-   Supports Docker image repository services based on Docker Registry V2.

    For example, image-syncer supports Docker Hub, Quay.io, Alibaba Cloud Container Registry, and Harbor.

-   Synchronizes images by using only the memory and network resources. Images are not stored on the disks of the server where the destination image repository resides. This improves the synchronization efficiency.
-   Supports incremental synchronization.

    image-syncer uses a file to record the blob information about synchronized images. Therefore, images that have been synchronized are not synchronized again.

-   Supports concurrent synchronization.

    You can modify the number of images that can be concurrently pulled or pushed in the configuration file.

-   Automatically retries failed synchronization tasks to resolve most image synchronization issues caused by network jitters.
-   Programs such as the Docker daemon are not required.

By using the image-syncer tool, you can migrate, copy, and perform incremental synchronization of images from an image repository. Ensure that image-syncer can communicate with the source and destination repositories. image-syncer has no requirements on hardware resources. However, the number of images that are concurrently synchronized must be equal to the number of network connections on image-syncer. The memory consumed by image-syncer is less than or equal to the product of the number of images that are concurrently synchronized and the size of the largest image layer. Therefore, the memory of the server that runs image-syncer may be exhausted only if the size of the largest image layer and the number of images that are concurrently synchronized are too large. In addition, image-syncer provides the retransmission mechanism to avoid occasional failures during synchronization. image-syncer counts the number of images that fail to be synchronized when synchronization ends and provides detailed logs to help you locate issues.

## Preparations

To use image-syncer, prepare only one configuration file. The following code block shows an example configuration file:

```
{
    "auth": {                   // The authentication information field. Each object contains the username and password that are required to access a registry.
                                // In most cases, image-syncer must have permissions to pull images from and access tags in the source registry.
                                // image-syncer must have permissions to push images to and create repositories in the destination registry. If no authentication information is provided for a registry, image-syncer accesses the registry in anonymous mode.

        "quay.io": {            // The URL of the registry, which must be the same as that of the registry in image URLs.
            "username": "xxx",               // Optional. The username.
            "password": "xxxxxxxxx",         // Optional. The password.
            "insecure": true                 // Optional. Specifies whether the repository is accessed through HTTP. Default value: false. Only image-syncer of V1.0.1 and later support this parameter.
        },
        "registry.cn-beijing.aliyuncs.com": {
            "username": "xxx",
            "password": "xxxxxxxxx"
        },
        "registry.hub.docker.com": {
            "username": "xxx",
            "password": "xxxxxxxxxx"
        }
    },
    "images": {
        // The field that describes image synchronization rules. Each rule is a key-value pair. The key specifies the URL of the source repository and the value specifies the URL of the destination repository.

        // You cannot synchronize an entire namespace or registry based on one rule. You can synchronize only one repository based on one rule.

        // The URLs of the source and destination repositories are in the format of registry/namespace/repository:tag, which is similar to the image URL format used in the docker pull or docker push command.
        // The URL of the source repository must contain registry/namespace/repository. If the URL of the destination repository is not an empty string, it must also contain registry/namespace/repository.
        // The URL of the source repository cannot be an empty string. To synchronize images from a source repository to multiple destination repositories, you must configure multiple rules.
        // The name and tags of the destination repository can be different from those of the source repository. In this case, the image synchronization rule works in the same way as the combination of the docker pull, docker tag, and docker push commands.
        "quay.io/coreos/kube-rbac-proxy": "quay.io/ruohe/kube-rbac-proxy",
        "xxxx":"xxxxx",
        "xxx/xxx/xx:tag1,tag2,tag3":"xxx/xxx/xx"
        // If the URL of the source repository does not contain tags, all images in the source repository are synchronized to the destination repository with the original tags. In this case, the URL of the destination repository cannot contain tags.
        // If the URL of source repository contains only one tag, only images that has this tag in the source repository are synchronized to the destination repository. If the URL of the destination repository does not contain a tag, synchronized images keep the original tag.
        // If the URL of the source repository contains multiple tags that are separated with commas (,), such as "a/b/c:1,2,3", the URL of the destination repository cannot contain tags. Synchronized images keep the original tags.

        // If the URL of the destination repository is an empty string, images are synchronized to a repository that has the same name and tags in the default namespace of the default registry. The default registry and namespace can be set through command parameters or environment variables.
    }     
}
```

## Examples

-   [Synchronize images from a user-created Harbor instance to Container Registry Default Instance Edition](/intl.en-US/Best Practices/Migrate a self-built Kubernetes cluster to Container Service for Kubernetes/Migrate container images/Synchronize images from self-built Harbor to Container Registry Default Instance Edition.md)
-   [Synchronize images from a user-created Harbor instance to Container Registry Enterprise Edition](/intl.en-US/Best Practices/Migrate a self-built Kubernetes cluster to Container Service for Kubernetes/Migrate container images/Synchronize images from self-built Harbor to Container Registry Enterprise Edition.md)

