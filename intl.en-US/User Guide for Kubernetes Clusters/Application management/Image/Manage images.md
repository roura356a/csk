---
keyword: [create images, view images]
---

# Manage images

This topic describes how to create and query images.

## Create images

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **Alibaba Cloud Container Registry**.

3.  In the Container Registry console, the **Repositories** page appears. Click **Create Repository**.

4.  In the **Create Repository** dialog box, set the required parameters for the repository.

    1.  On the **Repository Info** wizard page, set the required parameters and click **Next**.

    2.  On the **Code Source** wizard page, set the required parameters and click **Create Repository**. For more information about how to create an image repository, see [Create a repository and build images]().

        You can upload your own images from a local repository by using the command-line interface \(CLI\). You can also select a third-party code repository such as GitHub or Bitbucket to set up automatic image builds.


## Query images

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **Alibaba Cloud Container Registry**.

3.  On the **Repositories** page, click **Create Repository**.

    -   **Alibaba Cloud Container Registry**: On the Alibaba Cloud Container Registry tab, you can view the images that are stored in Container Registry.

        **Image Region**: Specify a region to filter images.

        **Container Registry Instance**: The default Container Registry instance is used.

    -   **Docker Official Images**: On the Docker Official Images tab, you can view images from Docker Hub.
    -   **Favorite Images**: On the Favorite Images tab, you can view images that you have specified as the favorite images on other tabs. For example, the images that you have specified as the favorite images on the [Container Registry Instance](https://cr.console.aliyun.com/) tab are synchronized to the Favorite Images tab.
    -   **Search**: On the Search tab, you can specify a region to search for an image in Container Registry. You can also select Docker Images from the drop-down list and search for Docker images.

