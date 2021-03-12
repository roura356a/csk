# Simplify Kubernetes application deployment by using Helm

This topic introduces the basic concepts and components of Helm and describes how to use Helm to deploy the sample applications WordPress and Spark in a Container Service for Kubernetes \(ACK\) cluster.

-   An ACK cluster is created in the ACK console. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

    Tiller is automatically deployed to the cluster when the ACK cluster is created. The Helm command-line interface \(CLI\) is automatically installed on each master node. You must configure the Helm CLI to point to the Alibaba Cloud chart repository.

-   The supported Kubernetes version is used.

    Only Kubernetes V1.8.4 and later are supported. For Kubernetes V1.8.1, you can upgrade the cluster to the required version. To upgrade the cluster, log on to the ACK console, go to the Clusters page, find the cluster, and then choose More \> **Upgrade Cluster** in the Actions column.


When you run and manage applications with ACK, you can use Helm as the package manager to simplify application distribution and deployment. The Helm project allows you to perform software packaging and supports version control. In the ACK console, the App Catalog feature integrates the Helm binaries and supports the Alibaba Cloud chart repository. This allows you to deploy applications by using the Helm CLI or in the ACK console.

## Overview

Helm is an open source tool that is created by Deis. It can be used to simplify the deployment and management of ACK applications.

Helm serves as an ACK package manager and allows you to find, share, and run applications that are created in ACK. When you use Helm, you must learn more about the following basic concepts:

-   Chart: a packaging format used by Helm. Each chart contains the images, dependencies, and resource definitions that are required to run an application. A chart may contain service definitions in an ACK cluster. You can use a chart by using a similar method as you use a Homebrew formula, the dpkg packages manager of the Advanced Package Tool \(APT\) package management system, or the Red Hat Package Manager \(RPM\) package for Yellowdog Updater, Modified \(YUM\).
-   Release: an instance of a chart that runs in an ACK cluster. A chart can be installed multiple times into the same cluster. After a chart is installed, a new release is created. For example, you can install a MySQL chart. If you want to run two databases in your cluster, you can install the MySQL chart twice. Each installation generates a release with a release name.
-   Repository: the location where charts are stored and released.

## Helm components

Helm works in a client-server architecture and consists of the following components:

-   The Helm CLI is the Helm client that runs on your on-premises computer or on the master nodes of an ACK cluster.
-   Tiller is the server-side component and runs in an ACK cluster. Tiller manages the lifecycles of ACK applications.
-   A repository is used to store charts. The Helm client can access the index file and packaged charts in a chart repository over HTTP.

## Deploy an application in the ACK console

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  For example, on the **Alibaba Cloud Apps** tab, click a chart, such as WordPress. Then, you are redirected to the page that shows more details of the WordPress chart.

4.  In the Deploy pane on the right side of the page, enter the basic information for the deployment.

    -   **Cluster**: Select the cluster to which you want to deploy the application.
    -   **Namespace**: Select a namespace for the application. By default, this parameter is set to default.
    -   **Release Name**: Enter a release name for the application.
    ![Basic deployment information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5145359951/p10397.png)

5.  Click the **Parameters** tab to set the parameters.

    In this example, a dynamically provisioned volume \(PV\) is associated with a persistent volume claim \(PVC\). For more information, see [Use Alibaba Cloud disks as volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Use Alibaba Cloud disks as volumes.md).

    **Note:** Before you associate the PV with the PVC, you must create a dynamically provisioned persistent volume \(PV\). The capacity of the PV cannot be less than the value that is defined by the PVC.

    ![Set the parameters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5145359951/p10398.png)

6.  After you set the parameters, click **Create** to deploy the application. After the application is deployed, you are redirected to the release page of the application.

7.  In the left-side navigation pane, choose **Clusters**. Find the cluster that you want to manage and click **Applications** in the **Actions** column. The Workload page appears. In the left-side navigation pane, click **Services**. On the Services page, find the newly created service. You can check the HTTP and HTTPS external endpoint of the service.

8.  Click either of the endpoints to go to the WordPress application where you can publish blog posts.


## Deploy an application by using the Helm CLI

After the Helm CLI is automatically installed on each master node of the ACK cluster and points to the required repository, you can log on to a master node by using SSH. This allows you to deploy applications by using the Helm CLI. For more information, see [t16642.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use SSH to connect to an ACK cluster.md). You can also install and configure the Helm CLI and kubectl on your on-premises computer.

In this example, on your on-premises computer, the Helm CLI and kubectl are installed and configured and the WordPress and Spark applications are deployed.

1.  Install and configure the Helm CLI and kubectl.

    1.  Install and configure kubectl on your on-premises computer.

        For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

        To view further details of the ACK cluster, enter `kubectl cluster-info` in the CLI of kubectl.

    2.  Install Helm on your on-premises computer.

        For more information, see [Install Helm](https://github.com/helm/helm).

2.  Deploy the WordPress application.

    To deploy a WordPress blog website by using Helm, perform the following steps:

    1.  In the CLI, run the following command:

        ```
        helm install --name wordpress-test stable/wordpress
        ```

        **Note:** ACK allows you to use block storage or disks as dynamically provisioned volumes. Before you deploy the WordPress application, you must create dynamically provisioned volumes based on disks.

        The following output is returned:

        ```
        NAME:   wordpress-test
        LAST DEPLOYED: Mon Nov  20 19:01:55 2017
        NAMESPACE: default
        STATUS: DEPLOYED
        ...
        ```

    2.  In the CLI, run the following commands to view the release and service of WordPress.

        ```
        helm list
        kubectl get svc
        ```

    3.  In the CLI, run the following command to view the pod that is associated with the WordPress application. The pod may require a few minutes to change to the Running state.

        ```
        kubectl get pod
        ```

    4.  In the CLI, run the following command to obtain the endpoint of the WordPress application.

        ```
        echo http://$(kubectl get svc wordpress-test-wordpress -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
        ```

        You can enter the preceding endpoint in your browser to access the WordPress application.

        You can perform the steps for the chart that are provided in the ACK console. Then, you can run the following commands to obtain the administrator account and password of the WordPress application:

        ```
        echo Username: user
        echo Password: $(kubectl get secret --namespace default wordpress-test-wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode)
        ```

    5.  In the CLI, run the following command to delete the WordPress application:

        ```
        helm delete --purge wordpress-test
        ```


## Use a third-party chart repository

You can use the default Alibaba Cloud chart repository. If a third-party chart repository is accessible, you can also use the third-party chart repository. In the CLI, run the following command to add a third-party chart repository:

```
helm repo add Repository name Repository URL
helm repo update
```

For more information about Helm commands, see [Helm documentation](https://docs.helm.sh/helm/#helm-repo-add).

## References

Helm provides several rapid technological developments for the Kubernetes community. These developments have allowed software providers, such as Bitnami, to offer high-quality charts. For more information about available charts, visit `https://kubeapps.com/`.

