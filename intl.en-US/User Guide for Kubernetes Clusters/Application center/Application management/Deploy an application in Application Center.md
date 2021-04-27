---
keyword: [Application Center, create an application, deploy an application]
---

# Deploy an application in Application Center

You can view the deployment state and changes of all Kubernetes resources when you deploy an application in Application Center in the Container Service for Kubernetes \(ACK\) console. You can choose different methods to deploy applications based on the data sources of the templates. Application Center allows you to use templates from Git repositories or ACK orchestration templates in the ACK console. This topic describes how to use different templates to deploy applications.

Before you deploy an application, make sure that you have completed the following steps:

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Install the Application Center controller](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Install the Application Center controller.md)

## Use Git repositories as data sources to deploy an application from a YAML template

You can use Git repositories as data sources to maintain and manage Git templates or Helm charts.

In this example, an application is deployed by using the [ingress-demo](https://github.com/xianlubird/argocd-example-apps/blob/master/ingress-demo/deploy.yaml) YAML file from a Git repository. Perform the following steps:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

3.  In the upper-right corner of the Application page, click **+ NEW APP** and set the parameters.

    The following tables describe the parameters.

    -   General settings

        ![Deploy an application](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1955359951/p112705.png)

        |Parameter|Description|
        |---------|-----------|
        |Application Name

|The name of the application. You can specify a custom name. |
        |SYNC POLICY

|The synchronization mode of the template. In this example, the manual mode is selected. Valid values:

        -   Manual: If the template on the Git repository is updated, you must click **SYNC** in Application Center to manually update the template and deploy the latest version of the application.
        -   Automatic: If the template in the Git repository is updated, ACK automatically updates the template and deploys the latest version of the application. |

    -   Source settings

        ![Source settings](../images/p112719.png)

        |Parameter|Description|
        |---------|-----------|
        |Data source

|The data source that is used to deploy an application. Valid values: GIT, HELM, and Custom template. In this example, **GIT** is selected. |
        |Repository URL

|The URL that is used to download the template from a Git repository. We recommend that you set this parameter to a value that starts with HTTPS.

In this example, the URL is set to [https://github.com/xianlubird/argocd-example-apps](https://github.com/xianlubird/argocd-example-apps.git). |
        |Revision

|The revision of the template. You can also specify a Git branch. In this example, the revision is set to HEAD. This indicates that the **master** branch is used. You can also specify other Git branches. |
        |Path

|The path where the template is stored. Set this parameter to a directory.

In this example, the path is set to ingress-demo.

**Note:** Do not set the path to the name of a file. |

    -   Destination settings

        ![Destination settings](../images/p112731.png)

        |Parameter|Description|
        |---------|-----------|
        |Cluster

|The cluster to which the application is deployed. In this example, the default cluster is selected. You can also select the cluster where you want to deploy the application. |
        |Namespace

|The namespace where the application is deployed. |
        |Directory/Helm/Kustomize

|Select a format for the template. In this example, Directory is selected.

        -   Directory: If you select Directory, you can specify whether to enable **directory recursion**. We recommend that you enable directory recursion if the path that stores the template has multiple levels.
        -   Helm:
            -   VALUES FILES: the file that contains the values to be specified. In this example, the default values.yaml file in the Helm chart is specified.
            -   VALUES: specify values. If you specify values, the values referenced from the values.yaml is overwritten by the specified values.
        -   Kustomize:
            -   NAME PREFIX: the prefix of a template that you want to use under the management of Kustomize. You can specify this parameter to search for templates that are named with the specified prefix.
            -   NAME SUFFIX: the suffix of a template that you want to use under the management of Kustomize. You can specify this parameter to search for templates that are named with the specified suffix. |

4.  Click **CREATE**.

    After the creation is complete, you can view the details of the application in Application Center.

    **Note:** After the creation is complete, Application Center parses the deployment template. However, the related resource objects are not deployed in the cluster. You can view the related resource objects in the deployment template.

5.  In the upper-right corner of the details page of the application, click **SYNC**. In the dialog box that appears, select the resources that you want to deploy and click **SYNC**.

    ![Resources to be deployed](../images/p112789.png)

    |Parameter|Description|
    |---------|-----------|
    |PRUNE

|**Deletes** the application resources that are not specified in the Git template. |
    |DRY RUN

|Parses the template only. The application resources specified in the template are not deployed in the cluster. |
    |APPLY ONLY

|Deploys the related resource objects that are specified in the template in the cluster. This option is applicable to most scenarios. |
    |FORCE

|Forcibly aligns the selected resource objects to those specified in the template. If the you select more than those specified in the template, the additional resource objects are removed from the deployment. If you select fewer, the unselected resource objects are added to the deployment. |
    |SYNCHRONIZE RESOURCES

|Lists all resource objects specified in the template. You can deploy certain or all resource objects based on your requirements. By default, all resource objects are deployed.

In this example, all resource objects are deployed. |

    In the upper-left corner of the details page of the application, click**APP DETAILS** to view the details. The following figure shows the details page of an application.

    ![The details page of the application](../images/p112821.png)


## Use Git repositories as data sources to deploy an application from a Helm chart

If you use a Helm chart from a Git repository to deploy an application, follow the steps as described in [Use Git repositories as data sources to deploy an application from a YAML template](#section_sbu_rur_agp).

![helm chart](../images/p112833.png "A Helm chart")

When you create an application, select **Helm** as the template format. You can set VALUES to different values for different clusters.

![helm](../images/p112841.png "Helm chart parameters")

## Use an ACK orchestration template to deploy an application

You can also use an ACK orchestration template in the ACK console to deploy an application. Perform the following steps:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **Orchestration Templates**.

3.  Select the template that you want to use and click **Create Application**.

4.  In the **Deploy** section, click **Create** and then click **Save**.

5.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

6.  Configure the parameters of the ACK orchestration template.

    When you create an application, select **Custom template** in the SOURCE section. Click the **Template** bar and select the template from the drop-down list. For more information about how to create an application, see [Use Git repositories as data sources to deploy an application from a YAML template](#section_sbu_rur_agp).

    ![Custom template](../images/p112865.png)


**Related topics**  


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Overview.md)

