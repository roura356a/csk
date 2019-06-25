# Create a workflow {#concept_269664 .concept}

This topic describes how to create a workflow by using the Container Service console or Ags CLI.

## Background information {#section_w94_vza_s8c .section}

Based on Argo, the workflows developed by Alibaba Cloud provide containerized workflows for Alibaba Cloud Container Service for Kubernetes. Specifically, a workflow is implemented as a Kubernetes Custom Resource Definition \(CRD\). As such, you can use kubectl to manage workflows, and integrate them with other Kubernetes services, such as volumes, Secrets, and Role-Based Access Control \(RBAC\). At the backend, the workflow controller provides complete workflow features such as parameter substitution, artifacts, fixtures, loops, and recursive workflows.

## Prerequisites {#section_v36_68t_8go .section}

-   A Kubernetes cluster is created. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   The Master node of the Kubernetes cluster is accessible. For more information, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

## Procedure {#section_0kc_ns8_rgh .section}

-   **Use the Container Service console to create a workflow named Hello World** 
    1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
    2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Workflow**.
    4.  In the upper-right corner, click **Create by Template**.
    5.  Set the template parameters.

        -   **Clusters**: Select the target cluster.
        -   **Namespaces**: Select the target namespace. The default namespace is used.
        -   **Sample Template**: Select a sample YAML template or customize a YAML template.

            **Note:** Alibaba Cloud Container Service for Kubernetes offers you with the sample YAML templates of various resources.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/222571/156143255247707_en-US.png)

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

    6.  Click **DEPLOY**.
    7.  On the Workflow page, find the target workflow. Then, in the **Action** column, click **Details** to view the overview and container group information of the workflow.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/222571/156143255347713_en-US.png)

-   **Use the command line interface \(CLI\) to create a workflow named Parameters** 

    **Note:** For more information about the CLI, see [Ags CLI](#title_yax_jf8_2ti).

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

You can create more workflows by modifying the sample workflow templates. For more information, see [Sample workflow templates](reseller.en-US/User Guide/Kubernetes cluster/Workflow/Sample workflow templates.md#).

## Ags CLI {#section_zey_8f4_yku .section}

Ags CLI is a CLI tool customized by Alibaba Cloud. This tool is compatible to Argo. With Ags CLI, you can submit, check, modify, and delete workflows.

To download a version of Ags CLI for your operating system, click [Linux](http://ags-hub.oss-cn-hangzhou.aliyuncs.com/ags-linux) or [Mac](http://ags-hub.oss-cn-hangzhou.aliyuncs.com/ags).

The following lists the available commands in Ags CLI:

``` {#codeblock_ybv_rsy_ss2}
ags is the command line interface to Alibaba Cloud Genomics Compute Service

Usage:
  ags [flags]
  ags [command]

Available Commands:
  add         ags add node
  completion  output shell completion code for the specified shell (bash or zsh)
  config      setup ags client necessary info
  delete      delete a workflow and its associated pods
  get         display details about a workflow
  help        Help about any command
  install     install ags
  kubectl     kubectl command
  lint        validate a file or directory of workflow manifests
  list        list workflows
  logs        view logs of a workflow
  remote      remote aliyun custom process
  resubmit    resubmit a workflow
  resume      resume a workflow
  retry       retry a workflow
  submit      submit a workflow
  suspend     suspend a workflow
  terminate   terminate a workflow
  uninstall   uninstall ags
  version     Print version information
  wait        waits for a workflow to complete
  watch       watch a workflow until it completes
```

