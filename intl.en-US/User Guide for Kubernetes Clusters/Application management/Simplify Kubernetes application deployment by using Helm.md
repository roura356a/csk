# Simplify Kubernetes application deployment by using Helm {#concept_fvz_4vm_vdb .concept}

In Kubernetes, app management is the most challenging and in demand field. The Helm project provides a uniform software packaging method which supports version control and greatly simplifies Kubernetes app distribution and deployment complexity.

Alibaba Cloud Container Service integrates the app catalog management function with the Helm tool, extends the functions, and supports official repository, allowing you to deploy the application quickly. You can deploy the application in the Container Service console or by using command lines.

This document introduces the basic concepts and usage of Helm and demonstrates how to use Helm to deploy the sample applications WordPress and Spark on an Alibaba Cloud Kubernetes cluster.

## Basic concepts of Helm {#section_sjv_cwm_vdb .section}

Helm is an open-source tool initiated by Deis and helps to simplify the deployment and management of Kubernetes applications.

You can understand Helm as a Kubernetes package management tool that facilitates discovery, sharing and use of apps built for Kubernetes. It involves several basic concepts.

-   **Chart**: A Helm package containing the images, dependencies, and resource definitions required for running an application. It may also contain service definitions in a Kubernetes cluster, similar to the formula of Homebrew, the dpkg of APT, or the rpm file of Yum.
-   **Release**: A chart running on a Kubernetes cluster. A chart can be installed multiple times on the same cluster. A new release will be created every time a chart is installed. For example, to run two databases on the server, you can install the MySQL chart twice. Each installation will generate its own release with its own release name.
-   **Repository**: The repository for publishing and storing charts.

## Helm components {#section_ujv_cwm_vdb .section}

Helm adopts a client/server architecture composed of the following components:

-   Helm CLI is the Helm client and can be run locally or on the master nodes of the Kubernetes cluster.
-   Tiller is the server component and runs on the Kubernetes cluster. It manages the lifecycles of Kubernetes applications.
-   Repository is the chart repository. The Helm client accesses the chart index files and packages in the repository by means of the HTTP protocol.

## Use Helm to deploy applications {#section_z1l_fwm_vdb .section}

**Prerequisites**

-   Before using Helm to deploy an application, create a Kubernetes cluster in Alibaba Cloud Container Service. For more information, see [Create a Kubernetes cluster](reseller.en-US//Create a Kubernetes cluster.md#).

    Tiller is automatically deployed to the cluster when the Kubernetes cluster is created. Helm CLI is automatically installed on all the master nodes and the configuration points to the Alibaba Cloud chart repository.

-   Check the Kubernetes version of your cluster.

    Only clusters whose Kubernetes version is 1.8.4 or later are supported. For clusters whose Kubernetes version is 1.8.1, **upgrade the cluster** on the Cluster List page.


## Deploy applications in Container Service console {#section_ozs_qxm_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click **Store** \> **App Catalog** in the left-side navigation pane.
3.  On the App Catalog page, click a chart \(WordPress in this example\) to enter the chart details page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16661/156464877810396_en-US.png)

4.  Enter the basic information for the deployment on the right.

    -   **Clusters**: Select the cluster in which the application is to be deployed.
    -   **Namespace**: Select the namespace. default is selected by default.
    -   **Release Name**: Enter the release name for the application. Enter test in this example.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16661/156464877910397_en-US.png)

5.  Click the **Values** tab to modify the configurations.

    In this example, bind dynamic data volumes of the cloud disk to a persistent storage volume claim \(PVC\). For more information, see .

    **Note:** You need to create a persistent storage volume \(PV\) of cloud disk in advance. The capacity of the PV cannot be less than the value defined by the PVC.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16661/156464877910398_en-US.png)

6.  Click **DEPLOY** after completing the configurations. After the successful deployment, you are redirected to the release page of this application.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16661/156464877910399_en-US.png)

7.  Click **Ingresses and Load Balancing** \> **Service** in the left-hand navigation pane. Select the target cluster and namespace and find the corresponding service. You can obtain the HTTP/HTTPS external endpoint address.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16661/156464877910400_en-US.png)

8.  Click the preceding access address to enter the WordPress blog publishing page.

## Deploy applications by using command lines {#section_ekv_cwm_vdb .section}

You can use SSH to log on to the master node of the Kubernetes cluster when deploying applications by using command lines \(Helm CLI is automatically installed and has configured the repository\). For more information, see [Access Kubernetes clusters by using SSH](reseller.en-US//Access a Kubernetes cluster by using SSH.md#). You can also install and configure the kubectl and Helm CLI locally.

In this example, install and configure the kubectl and Helm CLI locally and deploy the applications WordPress and Spark.

## Install and configure kubectl and Helm CLI {#section_fkv_cwm_vdb .section}

1.  Install and configure kubectl on a local computer.

    For more information, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US//Connect to a Kubernetes cluster by using kubectl.md#).

    To view information of the target Kubernetes cluster, enter the command `kubectl cluster-info`.

2.  Install Helm on a local computer.

    For the installation method, see [Install Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md?spm=5176.100239.blogcont159601.27.k76Hec&file=install.md).

3.  Configure the Helm repository. Here the charts repository provided by Alibaba Cloud Container Service is used.

    ``` {#codeblock_9x9_n2o_qwn}
    helm init --client-only --stable-repo-url https://aliacs-app-catalog.oss-cn-hangzhou.aliyuncs.com/charts/
    helm repo add incubator https://aliacs-app-catalog.oss-cn-hangzhou.aliyuncs.com/charts-incubator/
    helm repo update
    ```


**Basic operations of Helm**

-   To view the list of charts installed on the cluster, enter the following command:

    `helm list`

    Or you can use the abbreviated version:

    `helm ls`

-   To view the repository configurations, enter the following command:

    `helm repo list`

-   To view or search for the Helm charts in the repository, enter one of the following commands:

    ``` {#codeblock_bc9_w2m_4ro}
    helm search 
    helm search repository name #For example, stable or incubator.
    helm search chart name #For example, wordpress or spark.
    ```

-   To update the chart list to get the latest version, enter the following command:

    `helm repo update`


For more information about how to use Helm, see [Helm document](https://github.com/kubernetes/helm).

## Deploy WordPress by using Helm {#section_kkv_cwm_vdb .section}

Use Helm to deploy a WordPress blog website.

Enter the following command.

``` {#codeblock_qdj_0xt_f8n}
helm install --name wordpress-test stable/wordpress
```

**Note:** The Alibaba Cloud Kubernetes service provides the support for dynamic storage volumes of block storage \(cloud disk\). You need to create a storage volume of cloud disk in advance.

The result is as follows:

``` {#codeblock_obs_6o4_j71}
NAME: wordpress-test
LAST DEPLOYED: Mon Nov 20 19:01:55 2017
NAMESPACE: default
STATUS: DEPLOYED
...
```

Use the following command to view the release and service of WordPress.

``` {#codeblock_rte_8zw_s20}
helm list
kubectl get svc
```

Use the following command to view the WordPress related pods and wait until the status changes to Running.

``` {#codeblock_8in_3b3_1n4}
kubectl get pod
```

Use the following command to obtain the WordPress access address:

``` {#codeblock_x65_q0y_cmm}
echo http://$(kubectl get svc wordpress-test-wordpress -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
```

Access the preceding URL in the browser, and you can see the familiar WordPress website.

You can also follow the chart instructions and use the following command to obtain the administrator account and password of the WordPress website:

``` {#codeblock_r4n_13a_bz3}
echo Username: user
echo Password: $(kubectl get secret --namespace default wordpress-test-wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode)
```

To completely delete the WordPress application, enter the following command:

``` {#codeblock_j2u_scq_9n8}
helm delete --purge wordpress-test
```

## Deploy Spark by using Helm {#section_tkv_cwm_vdb .section}

Use Helm to deploy Spark for processing big data.

Enter the following command:

``` {#codeblock_srg_el5_75e}
helm install --name myspark stable/spark
```

The result is as follows:

``` {#codeblock_k2m_clz_9jl}
NAME: myspark
LAST DEPLOYED: Mon Nov 20 19:24:22 2017
NAMESPACE: default
STATUS: DEPLOYED
...
```

Use the following commands to view the release and service of Spark.

``` {#codeblock_i7k_583_0c1}
helm list
kubectl get svc
```

Use the following command to view the Spark related pods and wait until the status changes to Running. Pulling images takes some time because the Spark related images are large.

``` {#codeblock_xre_d7u_96z}
kubectl get pod
```

Use the following command to obtain the Spark Web UI access address:

``` {#codeblock_ae6_pqo_x4g}
echo http://$(kubectl get svc myspark-webui -o jsonpath='{.status.loadBalancer.ingress[0].ip}'):8080
```

Access the preceding URL in the browser, and you can see the Spark Web UI, on which indicating currently three worker instances exist.

Then, use the following command to use Helm to upgrade the Spark application and change the number of worker instances from three to four. The parameter name is case sensitive.

``` {#codeblock_34l_dcm_7wr}
helm upgrade myspark --set "Worker.Replicas=4" stable/spark
```

The result is as follows:

``` {#codeblock_uo4_l0g_9zz}
Release "myspark" has been upgraded. Happy Helming!
LAST DEPLOYED: Mon Nov 20 19:27:29 2017
NAMESPACE: default
STATUS: DEPLOYED
...
```

Use the following command to view the newly added pods of Spark and wait until the status changes to Running.

``` {#codeblock_lp3_s9x_bee}
kubectl get pod
```

Refresh the Spark Web UI in the browser. The number of worker instances changes to four.

To completely delete the Spark application, enter the following command:

``` {#codeblock_u4f_aar_gzu}
helm delete --purge myspark
```

## Use third-party chart repository {#section_elv_cwm_vdb .section}

Besides the preset Alibaba Cloud chart repository, you can also use the third-party chart repository \(make sure the network is accessible\). Add the third-party chart repository in the following command format:

``` {#codeblock_x14_kkz_d4y}
helm repo add repository name repository URL
helm repo update
```

For more information about the Helm related commands, see [Helm document](https://docs.helm.sh/helm/#helm-repo-add).

## References {#section_glv_cwm_vdb .section}

Helm boosts the growth of communities. More and more software providers, such as Bitnami, have begun to provide high-quality charts. You can search for and discover existing charts at `https://kubeapps.com/`.

