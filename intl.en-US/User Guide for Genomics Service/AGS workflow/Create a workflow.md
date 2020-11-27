---
keyword: [workflow, parameters workflow]
---

# Create a workflow

Based on Argo Workflows, workflows that are developed by Alibaba Cloud are used to implement containerized job orchestration in Kubernetes. Each step in a workflow is a container. This topic describes how to create a workflow in the Container Service for Kubernetes \(ACK\) console or by using the command-line interface \(CLI\).

-   [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md)

Based on Argo Workflows, workflows that are developed by Alibaba Cloud are used to implement containerized job orchestration in Kubernetes. Each step in a workflow is a container.

Each workflow is implemented as a Kubernetes CustomResourceDefinition\(CRD\). Therefore, you can use kubectl to manage workflows and integrate them with other Kubernetes services, such as volumes, Secrets, and role-based access control \(RBAC\). At the backend, the workflow controller provides a complete list of workflow features, such as parameter substitution, storage, loops, and recursion.

You can create workflows in the ACK console or by using the CLI.

## Create workflows in the ACK console

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Releases**.

5.  On the Releases page, click the **Workflows** tab. On the upper-right corner of the Workflows tab, click **Create**.

6.  In the Create from Template dialog box, set the parameters, configure the template, and then click **Create**.

    -   **Cluster**: Select a cluster. The workflow will be deployed in the selected cluster.
    -   **Namespace**: Select the namespace to which the workflow belongs. By default, the default namespace is selected.
    -   **Sample Template**: ACK provides YAML templates of various resource types to simplify the deployment of workflows. You can also create a custom template based on YAML syntax to describe the resource that you want to define.
    The following example is a custom template that is used to create a Hello World workflow:

    ```
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow                  # new type of k8s spec
    metadata:
      generateName: hello-world-    # name of the workflow spec
    spec:
      entrypoint: whalesay          # invoke the whalesay template
      templates:
      - name: whalesay              # name of the template
        container:
          image: docker/whalesay
          command: [cowsay]
          args: ["hello world"]
          resources:                # limit the resources
            limits:
              memory: 32Mi
              cpu: 100m
    ```

7.  After the workflow is created, you can view the created workflow on the Workflows tab.

    ![View the created workflow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6386858951/p47713.png)

    You can click **Details** in the Actions column to view information about the workflow and container groups.


## Create parameters workflows by using the CLI

1.  Create a YAML file named arguments-parameters.yaml and paste the following content into the file:

    ```
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow
    metadata:
      generateName: hello-world-parameters-
    spec:
      # invoke the whalesay template with
      # "hello world" as the argument
      # to the message parameter
      entrypoint: whalesay
      arguments:
        parameters:
        - name: message
          value: hello world
    
      templates:
      - name: whalesay
        inputs:
          parameters:
          - name: message       # parameter declaration
        container:
          # run cowsay with that message input parameter as args
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

