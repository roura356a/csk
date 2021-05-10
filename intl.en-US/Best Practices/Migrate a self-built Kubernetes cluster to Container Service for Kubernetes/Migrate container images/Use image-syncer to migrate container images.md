---
keyword: [image-syncer, migrate container images]
---

# Use image-syncer to migrate container images

Image migration and synchronization between image repositories are required to migrate applications from a self-managed Kubernetes cluster to a Container Service for Kubernetes \(ACK\) cluster. You can use image-syncer to migrate and synchronize multiple images from self-managed image repositories to Alibaba Cloud Container Registry \(ACR\) on the fly at a time. This topic describes how to use image-syncer to migrate container images.

Compared with the Kubernetes clusters created and maintained by other cloud providers, ACK is superior in terms of service costs, maintenance expenses, ease-of-use, and long-term stability. A growing number of cloud providers want to migrate their Kubernetes workloads to ACK. If the number of images is small, you can run the docker pull and docker push commands to migrate the images. If you run the commands to migrate more than a hundred images or an image repository that stores TB-level data, the migration process takes a long time and may cause data loss. In this case, the image synchronization capability is required for migrating images between image repositories. The open source tool image-syncer developed by Alibaba Cloud provides this capability and has helped many cloud service providers migrate images. The maximum image repository capacity is larger than 3 TB. The server that runs image-syncer can make full use of the server bandwidth, and no requirement exists for the disk capacity of the server.

## image-syncer overview

Image migration and synchronization between image repositories are required to migrate applications from a self-managed Kubernetes cluster to an ACK cluster. The traditional image synchronization method uses a script that contains the docker pull or docker push command and has the following limits:

-   You must remove existing images from the disks of the server where the destination image repository resides and store the migrated images on these disks. Therefore, this method does not apply to large-scale image migration.
-   The Docker daemon is required. The Docker daemon limits the number of images that can be pulled or pushed concurrently. As a result, you cannot perform high-concurrency image synchronization.
-   HTTP API operations are required to implement some features. You cannot synchronize images by using only the Docker CLI. As a result, you must write a complex synchronization script.

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

By using the image-syncer tool, you can migrate, copy, and perform incremental synchronization of images from an image repository. Make sure that image-syncer can communicate with the source and destination repositories. image-syncer has no requirements on hardware resources. However, the number of images that are concurrently synchronized must be equal to the number of network connections on image-syncer. The memory consumed by image-syncer is less than or equal to the product of the number of images that are concurrently synchronized and the size of the largest image layer. Therefore, the memory of the server that runs image-syncer may be exhausted only if the size of the largest image layer and the number of images that are concurrently synchronized are too large. In addition, image-syncer provides a retransmission mechanism to avoid occasional failures during synchronization. image-syncer counts the number of images that fail to be synchronized when synchronization ends and provides detailed logs to help you locate issues.

## Preparations

To use image-syncer, prepare a configuration file. The following code block shows an example of the configuration file:

```
{
    "auth": {                   // This field specifies the authentication information. Each object consists of a username and a password that are required to access a registry.
                                // Typically, image-syncer must have the permissions to pull images from and access tags in the source repository.                                                                                     // image-syncer must have permissions to push images to and create repositories in the destination registry. If these permissions are not provided, image-syncer access repositories in anonymous mode. 

        "quay.io": {            // The URL of the registry. The URL must be the same as that of the registry in image URLs. 
            "username":  "xxx",               // The username, which is optional.
            "password": "xxxxxxxxx",         // The password, which is optional.            "insecure": true                 // This parameter specifies whether the repository support HTTP. A value of true specifies that the repository supports HTTP. Default value: false. This parameter is optional and available to only image-syncer versions that are later than 1.0.1.
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
        // This field specifies the rules for synchronizing images. Each rule is a key-value pair. The key specifies the URL of the source image repository and the value specifies the URL of the destination image repository.

        // You can use rules to synchronize only image repositories. You cannot use a rule to synchronize a namespace or a registry.

        // The URLs of the source and destination repositories are in the format of registry/namespace/repository:tag, which is similar to the image URL format used in the docker pull or docker push command.
        // The URL of the source repository must contain registry/namespace/repository. If the URL of the destination repository is not an empty string, it must also contain registry/namespace/repository.
        // The URL of the source repository cannot be an empty string. To synchronize images from a source repository to multiple destination repositories, you must configure multiple rules.
        // The name and tags of the destination repository can be different from those of the source repository. In this case, the image synchronization rule works in the same way as the combination of the docker pull, docker tag, and docker push commands.        "quay.io/coreos/kube-rbac-proxy": "quay.io/ruohe/kube-rbac-proxy",
        "quay.io/coreos/kube-rbac-proxy": "quay.io/ruohe/kube-rbac-proxy",
        "xxxx":"xxxxx",
        "xxx/xxx/xx:tag1,tag2,tag3":"xxx/xxx/xx"
        // If the URL of the source repository does not contain tags, all images in the source repository are synchronized to the destination repository with the original tags. In this case, the URL of the destination repository cannot contain tags.
        // If the URL of source repository contains only one tag, only images that have this tag in the source repository are synchronized to the destination repository. If the URL of the destination repository does not contain a tag, synchronized images keep the original tag.
        // If the URL of the source repository contains multiple tags that are separated with commas (,), such as "a/b/c:1,2,3", the URL of the destination repository cannot contain tags. Synchronized images keep the original tags.

        // If the URL of the destination repository is an empty string, images are synchronized to a repository that has the same name and tags in the default namespace of the default registry. The default registry and namespace can be set through command parameters or environment variables.
    }
} {
            "username": "xxx",
            "password": "xxxxxxxxx"
        },
        "registry.hub.docker.com": {
            "username": "xxx",
            "password": "xxxxxxxxxx"
        }
    },
    "images": {
        // 同步镜像规则字段，其中一条规则包括一个源仓库（键）和一个目标仓库（值）

        // 同步的最大单位是仓库（repo），不支持通过一条规则同步整个namespace以及registry

        // 源仓库和目标仓库的格式与docker pull/push命令使用的镜像url类似（registry/namespace/repository:tag）
        // 源仓库和目标仓库（如果目标仓库不为空字符串）都至少包含registry/namespace/repository
        // 源仓库字段不能为空，如果需要将一个源仓库同步到多个目标仓库需要配置多条规则
        // 目标仓库名可以和源仓库名不同（tag也可以不同），此时同步功能类似于：docker pull + docker tag + docker push
        "quay.io/coreos/kube-rbac-proxy": "quay.io/ruohe/kube-rbac-proxy",
        "xxxx":"xxxxx",
        "xxx/xxx/xx:tag1,tag2,tag3":"xxx/xxx/xx"
        // 当源仓库字段中不包含tag时，表示将该仓库所有tag同步到目标仓库，此时目标仓库不能包含tag
        // 当源仓库字段中包含tag时，表示只同步源仓库中的一个tag到目标仓库，如果目标仓库中不包含tag，则默认使用源tag
        // 源仓库字段中的tag可以同时包含多个（比如"a/b/c:1,2,3"），tag之间通过","隔开，此时目标仓库不能包含tag，并且默认使用原来的tag

        // 当目标仓库为空字符串时，会将源镜像同步到默认registry的默认namespace下，并且repo以及tag与源仓库相同，默认registry和默认namespace可以通过命令行参数以及环境变量配置，参考下面的描述
    }     
}
```

## 使用示例

-   [Synchronize images from a self-managed Harbor instance to Container Registry Default Instance Edition](/intl.en-US/Best Practices/Migrate a self-built Kubernetes cluster to Container Service for Kubernetes/Migrate container images/Synchronize images from a self-managed Harbor instance to Container Registry Default Instance Edition.md)
-   [Synchronize images from a self-managed Harbor project to Container Registry Enterprise Edition](/intl.en-US/Best Practices/Migrate a self-built Kubernetes cluster to Container Service for Kubernetes/Migrate container images/Synchronize images from a self-managed Harbor project to Container Registry Enterprise Edition.md)

