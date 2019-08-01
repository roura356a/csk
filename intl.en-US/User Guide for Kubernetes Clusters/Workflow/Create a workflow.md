# Create a workflow {#concept_269664 .concept}

This topic describes how to create a workflow by using the Container Service console or Ags CLI.

## Background information {#section_w94_vza_s8c .section}

Based on Argo, the workflows developed by Alibaba Cloud provide containerized workflows for Alibaba Cloud Container Service for Kubernetes. Specifically, a workflow is implemented as a Kubernetes Custom Resource Definition \(CRD\). As such, you can use kubectl to manage workflows, and integrate them with other Kubernetes services, such as volumes, Secrets, and Role-Based Access Control \(RBAC\). At the backend, the workflow controller provides complete workflow features such as parameter substitution, artifacts, fixtures, loops, and recursive workflows.

## Prerequisites {#section_v36_68t_8go .section}

-   A Kubernetes cluster is created. For more information, see [Create a Kubernetes cluster](intl.en-US//Create a Kubernetes cluster.md#).
-   The Master node of the Kubernetes cluster is accessible. For more information, see [Connect to a Kubernetes cluster by using kubectl](intl.en-US//Connect to a Kubernetes cluster by using kubectl.md#).

## Procedure {#section_0kc_ns8_rgh .section}

-   **Use the Container Service console to create a workflow named Hello World** 
    1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
    2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Workflow**.
    3.  In the upper-right corner, click **Create by Template**.
    4.  Set the template parameters.

        -   **Clusters**: Select the target cluster.
        -   **Namespaces**: Select the target namespace. The default namespace is used.
        -   **Sample Template**: Select a sample YAML template or customize a YAML template.

            **Note:** Alibaba Cloud Container Service for Kubernetes offers you with the sample YAML templates of various resources.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/222571/156464855447707_en-US.png)

        The following is a sample YAML template of the workflow named Hello World:

        ``` {#codeblock_y3z_n8o_uet}
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

    5.  Click **DEPLOY**.
    6.  On the Workflow page, find the target workflow. Then, in the **Action** column, click **Details** to view the overview and container group information of the workflow.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/222571/156464855447713_en-US.png)

-   **Use the command line interface \(CLI\) to create a workflow named Parameters** 

    **Note:** Ags CLI is a CLI tool customized by Alibaba Cloud. This tool is compatible to Argo. With Ags CLI, you can submit, check, modify, and delete workflows. For more information, see [Introduction to AGS CLI](intl.en-US/User Guide for Kubernetes Clusters/Workflow/Introduction to AGS CLI.md#).

    1.  Create the file arguments-parameters.yaml, and then copy the following code to the file:

        ``` {#codeblock_cay_tin_qra}
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

    2.  Run the `ags submit arguments-parameters.yaml -p message="goodbye world"` command.

You can create more workflows by modifying the sample workflow templates. For more information, see [Sample workflow templates](intl.en-US/User Guide for Kubernetes Clusters/Workflow/Sample workflow templates.md#).

