# Use a GPU container instance {#concept_265436 .concept}

This topic describes how to use a GPU container instance by using an example in which Tensorflow is used to identify a picture. ACK serverless Kubernetes provides GPU container instances by integrating with Elastic Container Instance \(ECI\). As a part of this service, GPU container instances can be used in a serverless Kubernetes cluster or on a virtual node of a dedicated or managed Kubernetes cluster.

## Billing {#section_tmr_txo_ab8 .section}

GPU and CPU resources provided by ACK serverless Kubernetes are charged with the Pay-As-You-Go billing method.

GPU instances provided by ACK serverless Kubernetes are charged the same as the ECS GPU instances. You are not charged additional fees.

## Overview {#section_71f_08l_y7q .section}

In an ACK serverless Kubernetes cluster, you can easily create a pod to which a GPU is attached. Specifically, for the pod configurations, you must set the `annotations` parameter to specify the GPU type that you want, and set the `limits` parameter to specify the number of GPU.

**Note:** A GPU is exclusive to one pod.

## Prerequisites {#section_ksf_ln9_lil .section}

Either a serverless Kubernetes cluster is created, or a virtual node is created for a Kubernetes cluster. For more information, see [Create a serverless Kubernetes cluster](../../../../reseller.en-US/User Guide/Serverless Kubernetes cluster/Cluster management/Create a serverless Kubernetes cluster.md#) or [Deploy a virtual node](../../../../reseller.en-US/User Guide/Kubernetes cluster/Auto Scaling/Deploy a virtual node.md#).

## Procedure {#section_6au_dph_ipb .section}

In the following steps, Tensorflow is used in a serverless Kubernetes cluster to identify the following picture.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220322/155952850047460_en-US.png)

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Deployments**. Then, in the upper-right corner, click **Create by Template**.
4.  Select the target cluster and namespace, select an example template or customize a template, and then click **DEPLOY**.

    You can use the following YAML template to create a pod:

    **Note:** In this pod template, the GPU type is set to P4, and the number of GPU is set to 1.

    ``` {#codeblock_jzu_9rq_iwl}
    apiVersion: v1
    kind: Pod
    metadata:
      name: tensorflow
      annotations:
        k8s.aliyun.com/eci-gpu-type : "P4"
    spec:
      containers:
      - image: registry-vpc.cn-hangzhou.aliyuncs.com/ack-serverless/tensorflow
        name: tensorflow
        command:
        - "sh"
        - "-c"
        - "python models/tutorials/image/imagenet/classify_image.py"
        resources:
          limits:
            nvidia.com/gpu: "1"
      restartPolicy: OnFailure
    ```

5.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Pods** to verify that the pod is created.

    **Note:** It may take a few minutes to create a pod. If you the pod is not displayed, you can refresh the page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220322/155952850047462_en-US.png)

6.  Click the target pod, and click the **Logs** tab to verify that the picture is identified according to the logs marked in the red frame.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220322/155952850047463_en-US.png)


If you want to use a GPU container instance on a virtual node of a dedicated or managed Kubernetes cluster, follow these steps:

**Note:** A GPU container instance used on a virtual node supports a larger variety of deep learning platforms such as Kubeflow, Arena, or other customized CRD.

1.  Create a pod on the virtual node or in a namespace to which the virtual-node-affinity-injection=enabled label tag is added. For more information, see [Deploy a virtual node](../../../../reseller.en-US/User Guide/Kubernetes cluster/Auto Scaling/Deploy a virtual node.md#).
2.  Replace the template used in step [4](#li_cik_5iq_2rk) with the following template:

    ``` {#codeblock_wet_enm_58k}
    apiVersion: v1
    kind: Pod
    metadata:
      name: tensorflow
      annotations:
        k8s.aliyun.com/eci-gpu-type : "P4"
    spec:
      containers:
      - image: registry-vpc.cn-hangzhou.aliyuncs.com/ack-serverless/tensorflow
        name: tensorflow
        command:
        - "sh"
        - "-c"
        - "python models/tutorials/image/imagenet/classify_image.py"
        resources:
          limits:
            nvidia.com/gpu: "1"
      restartPolicy: OnFailure
      nodeName: virtual-kubelet
    ```


