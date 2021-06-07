# Use Helm to simplify application deployment

This topic introduces the basic concepts of Helm and describes how to use Helm to deploy an Apache Spark-based WordPress application in a Container Service for Kubernetes \(ACK\) cluster.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

    Tiller is automatically deployed to the cluster when the ACK cluster is created. Helm command-line interface \(CLI\) is automatically installed on each master node. An Alibaba Cloud chart repository is added to Helm.

-   A Kubernetes version that supports Helm is used.

    Only Kubernetes 1.8.4 and later support Helm. If the Kubernetes version of your cluster is V1.8.1, you can **upgrade** the cluster on the Clusters page of the ACK console.


Application management is the most challenging task in Kubernetes. The Helm project provides a unified method to package software and manage software versions. You can use Helm to simplify application distribution and deployment. App Catalog is integrated with Helm in the ACK console and provides extended features based on Helm. App Catalog also supports Alibaba Cloud chart repositories to help you accelerate application deployments. You can deploy applications in the ACK console or by using Helm CLI.

## Basic concepts of Helm

Helm is an open source project initiated by Deis. Helm can be used to simplify the deployment and management of Kubernetes applications.

Helm serves as a package manager for Kubernetes and allows you to find, share, and use applications built by using Kubernetes. When you use Helm, you must review the following concepts:

-   Chart: a packaging format used by Helm. Each chart contains the images, dependencies, and resource definitions that are required to run an application. A chart may contain service definitions in a Kubernetes cluster. A Helm chart is similar to a Homebrew formula, an Advanced Package Tool \(APT\) dpkg, or a Yum rpm.
-   Release: an instance of a chart that runs in a Kubernetes cluster. A chart can be installed multiple times into a Kubernetes cluster. After a chart is installed, a new release is created. For example, you can install a MySQL chart. If you want to run two databases in your cluster, you can install the MySQL chart twice. Each time a chart is installed, a release is created with a different name.
-   Repository: the storage of charts. Charts are published and stored in repositories.

## Helm components

Helm uses a client-server architecture and consists of the following components:

-   Helm CLI is the Helm client that runs on your on-premises machine or on the master node of a Kubernetes cluster.
-   Tiller is the server-side component and runs in a Kubernetes cluster. Tiller manages the lifecycles of Kubernetes applications.
-   A repository is used to store charts. The Helm client can access the index file and packaged charts in a chart repository over HTTP.

## Deploy an application in the ACK console

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **Alibaba Cloud Apps** tab, select and click a chart to go to the details page. In this example, **ack-wordpress-sample** is used.

4.  Click the **Parameters** tab and modify the parameters.

    In this example, a persistent volume claim \(PVC\) is specified to bind a dynamic disk volume. For more information, see [Usage notes for disk volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Usage notes for disk volumes.md).

    **Note:** You must first provision a disk as a persistent volume \(PV\). The volume size claimed in the PVC cannot exceed the capacity of the PV.

    ![Set the parameters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5145359951/p10398.png)

5.  On the right side of the page, configure the basic settings and click **Create**.

    -   **Cluster**: the cluster where you want to deploy the application.
    -   **Namespace**: the namespace where you want to deploy the application. By default, the default namespace is selected.
    -   **Release Name**: the release name for the application.
    ![Basic settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5145359951/p10397.png)

6.  In the left-side navigation pane of the details page, choose **Network** \> **Services**.

7.  In the Service list, you can find the Service created for the application and the corresponding external endpoints through HTTP and HTTPS. Click an external endpoint to access the WordPress blog page.

    **Note:** Before you access the external endpoint, make sure that the port of the endpoint is added to the security group.


## Deploy an application by using Helm CLI

After Helm CLI is automatically installed in the ACK cluster and the required chart repository is added to Helm, you can log on to the cluster by using SSH. Then, you can deploy applications by using Helm CLI. For more information, see [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use SSH to connect to an ACK cluster.md). You can also install and configure Helm CLI and kubectl on your on-premises machine.

In this example, Helm CLI and kubectl are installed and configured on your on-premises machine, and then an Apache Spark-based WordPress application is deployed.

1.  Install and configure Helm CLI and kubectl.

    1.  Install and configure kubectl on your on-premises machine.

        For more information, see [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

        To view the details of a Kubernetes cluster, run the `kubectl cluster-info` command.

    2.  Install Helm on your on-premises machine.

        For more information, see [Install Helm](https://github.com/helm/helm).

2.  Deploy a WordPress application.

    In the following example, a WordPress blog website is deployed by using Helm.

    1.  Run the following command:

        ```
        helm install --name wordpress-test stable/wordpress
        ```

        **Note:** ACK supports dynamic disk volumes. You must first provision a disk as a PV.

        The following output is returned:

        ```
        NAME:   wordpress-test
        LAST DEPLOYED: Mon Nov  20 19:01:55 2017
        NAMESPACE: default
        STATUS: DEPLOYED
        ...
        ```

    2.  Run the following commands to query the release and Service created for the WordPress application:

        ```
        helm list
        kubectl get svc
        ```

    3.  Run the following command to view the pods provisioned for the WordPress application. You may need to wait before the pods change to the Running state.

        ```
        kubectl get pod
        ```

    4.  Run the following command to obtain the endpoint of the WordPress application:

        ```
        echo http://$(kubectl get svc wordpress-test-wordpress -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
        ```

        You can enter the preceding URL into the address bar of your browser to access the WordPress application.

        You can also run the following commands based on the chart description to obtain the username and password of the administrator for the WordPress application:

        ```
        echo Username: user
        echo Password: $(kubectl get secret --namespace default wordpress-test-wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode)
        ```

    5.  To delete the WordPress application, run the following command:

        ```
        helm delete --purge wordpress-test
        ```


## Use a third-party chart repository

You can use the default Alibaba Cloud chart repository. If a third-party chart repository is accessible from your cluster, you can also use the third-party chart repository. Run the following command to add a third-party chart repository to Helm:

```
helm repo add Repository name Repository URL
helm repo update
```

For more information about Helm commands, see [Helm documentation](https://docs.helm.sh/helm/#helm-repo-add).

## References

Helm contributes to the development of Kubernetes. A growing number of software suppliers, such as Bitnami, have provided high-quality charts. For more information about available charts, visit `https://kubeapps.com/`.

