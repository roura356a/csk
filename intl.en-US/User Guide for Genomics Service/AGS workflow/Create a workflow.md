---
keyword: [workflow, parameters workflow]
---

# Create a workflow

Workflows developed by Alibaba Cloud are developed based on Argo Workflows and are used to implement container orchestration in Kubernetes. Each step in a workflow is a container. This topic describes how to create a workflow in the Container Service for Kubernetes \(ACK\) console or by using a command-line interface \(CLI\).

-   A Container Service for Kubernetes \(ACK\) cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   You are connected to the cluster by using kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).
-   The workflow feature is enabled for your account.

    **Note:** To apply for the workflow feature to be enabled for your account,[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).


Workflows developed by Alibaba Cloud are developed based on Argo Workflows and are used to implement container orchestration in Kubernetes. Each step in a workflow is a container.

Workflows are implemented by using Kubernetes CustomResourceDefinitions\(CRDs\). Therefore, you can use kubectl to manage workflows and integrate them with other Kubernetes services, such as volumes, Secrets, and role-based access control \(RBAC\). At the backend, the workflow controller provides a complete set of workflow features, such as parameter substitution, storage, loops, and recursion.

You can create workflows in the ACK console or by using a CLI.

## Create a workflow in the ACK console

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Releases**.

5.  On the Releases page, click the **Workflows** tab. In the upper-right corner of the Workflows tab, click **Create**.

6.  Configure the template and click **Create**.

    -   **Cluster**: Select the cluster where the workflow is to be created. The workflow will be deployed in the selected cluster.
    -   **Namespace**: Select the namespace to which the workflow belongs. By default, the default namespace is selected.
    -   **Sample Template**: ACK provides YAML templates of various resource types to simplify the deployment of workflows. You can also create a custom template based on YAML syntax to customize resource definition.
    The following example is a custom template that is used to create a Hello World workflow:

    ```
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow                  # The type of Kubernetes object.
    metadata:
      generateName: hello-world-    # The name of the workflow.
    spec:
      entrypoint: whalesay          # Invoke the template of a whalesay image.
      templates:
      - name: whalesay              # The name of the template.
        container:
          image: docker/whalesay
          command: [cowsay]
          args: ["hello world"]
          resources:                # The resource limits.
            limits: 
              memory: 32Mi
              cpu: 100m
    ```

7.  After the workflow is created, you can view the created workflow on the Workflows tab.

    ![Successful creation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6386858951/p47713.png)

    You can click **Details** in the Actions column to view information about the workflow and pods.


## Create a parameters workflow by using a CLI

1.  Create a YAML file named arguments-parameters.yaml and copy the following content into the file:

    ```
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow
    metadata:
      generateName: hello-world-parameters-
    spec:
      # Specify "hello world" as the value of the parameter when the template of a whalesay image is invoked. 
      entrypoint: whalesay
      arguments:
        parameters:
        - name: message
          value: hello world
    
      templates:
      - name: whalesay
        inputs:
          parameters:
          - name: message       # The description of the parameter. 
        container:
          # Use the input parameter args to run the cosway command. 
          image: docker/whalesay 
          command: [cowsay]
          args: ["{{inputs.parameters.message}}"]
    ```

2.  Run the following command to deploy the parameters workflow:

    ```
    ags submit arguments-parameters.yaml -p message="goodbye world"
    ```


You can also use workflow templates provided in [Sample workflow templates](/intl.en-US/User Guide for Genomics Service/AGS workflow/Sample workflow templates.md) to create other types of workflows.

Ags CLI is a command-line interface developed by Alibaba Cloud. It is compatible with open source Argo. Ags CLI provides an easy way to submit, check, modify, and delete workflows. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md).

