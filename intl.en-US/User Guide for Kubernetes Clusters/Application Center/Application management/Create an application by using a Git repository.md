---
keyword: [Application Center, Git repository, create an application]
---

# Create an application by using a Git repository

You can view the deployment status and changes of all Kubernetes resources when you deploy an application in Application Center in the Container Service for Kubernetes \(ACK\) console. You can choose different methods to deploy applications based on the data sources of the templates. This topic describes how to create an application by using a Git repository.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

## Sample repository address

[GitHub repository address](https://github.com/AliyunContainerService/appcenter-samples.git)

## Application orchestration methods used by Git repositories

-   Kubernetes-native manifests

    ```
    .
    ├── deployment.yaml
    ├── ingress.yaml
    └── service.yaml
    ```

-   Helm charts

    ```
    .
    ├── Chart.yaml
    ├── templates
    │   ├── deployment.yaml
    │   ├── _helpers.tpl
    │   ├── ingress.yaml
    │   ├── NOTES.txt
    │   ├── serviceaccount.yaml
    │   ├── service.yaml
    │   └── tests
    │       └── test-connection.yaml
    ├── values-pro.yaml
    └── values.yaml
    ```

-   Kustomize

    ```
    .
    ├── base
    │   ├── deployment.yaml
    │   ├── ingress.yaml
    │   ├── kustomization.yaml
    │   └── service.yaml
    └── overlays
        ├── pre
        │   ├── deployment.yaml
        │   └── kustomization.yaml
        └── pro
            ├── deployment.yaml
            └── kustomization.yaml
    ```


## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

3.  On the Application Center page, click **Create Application**.

4.  In the **Source** step, set the parameters.

    1.  In the **Install appcenter** section, select the cluster where you want to install appcenter and click **Install**.

        **Note:** This step is required only when you use Application Center to create applications for the first time.

    2.  In the **Source** section, click **Git Repository**.

    3.  Click **Next**.

5.  In the **Application Configurations** step, set the parameters as described in the following table and click **Next**.

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the application|
    |Description|The description of the application|
    |Repository|Select the source code repository. You can select an existing repository or create one. In this topic, a repository is created.|
    |Repository Address|The address of the repository|
    |Name|The name of the repository|
    |Connection Method|Select a connection method. **HTTPS** and **SSH** are supported. The following list describes the parameters used by each connection method:    -   If you select **HTTPS**, the following parameters are used:
        -   Username: You must specify a username if you use a private Git repository.
        -   Password: You must specify a password if you use a private Git repository.
        -   Client TLS Certificate \(Optional\): If the Git server uses a self-signed certificate, you must specify a client Transport Layer Security \(TLS\) certificate.
        -   Client TLS Key \(Optional\): If the Git server uses a self-signed certificate, you must specify a TLS key.
        -   Skip Server Certificate Verification: If the Git server uses a self-signed certificate and you want to skip server certificate verification, you can select Enable to skip the configurations of the client TLS certificate and client TLS key.
        -   Support for LFS: Specify whether to enable Large File Storage \(LFS\) for the Git repository.
    -   If you select **SSH**, the following parameters are used:

SSH Private Key: Specify the SSH private key. |
    |Branch|The name of the branch that you want to use|
    |Path|The subdirectory of the application orchestration files in the Git repository|

6.  Set parameters in the **publish configuration** wizard page.

    1.  Select the publishing method in the **publishing configuration** area, including full volume publishing and grayscale publishing.

        **Note:** Only **full release** can be selected when publishing applications for the first time. **Full release** or **gray release** can be selected when updating and iterating applications later.

    2.  In the **optional cluster** area, click **add** on the left of the cluster. You can see the selected cluster in the **target cluster** area, and then select the **namespace** to be published in the **target cluster** area.

        **Note:** If there is no cluster with public network access, EIP needs to be bound for API server to enable public network access.

    3.  Click **start publishing**.

7.  Click OK in the **publish confirmation** dialog box.

    You can see the progress of publishing applications in the **publishing log** configuration page.


**Related topics**  


[Application Center overview](/intl.en-US/User Guide for Kubernetes Clusters/Application Center/Application Center overview.md)

[t2067143.md\#]()

[Create an application from an orchestration template](/intl.en-US/User Guide for Kubernetes Clusters/Application Center/Application management/Create an application from an orchestration template.md)

