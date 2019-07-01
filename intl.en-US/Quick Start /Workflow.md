# Workflow {#concept_vdx_ck4_m2b .concept}

This topic describes the workflow of how to create an application in the Container Service console. This workflow involves the steps required to create an application.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15912/15619672657633_en-US.png)

To create an application, follow these steps:

1.  **Use a RAM role to grant the corresponding permissions to a user account** 

    For more information, see [Role authorization](../../../../intl.en-US/User Guide/Kubernetes cluster/Authorization management/Role authorization.md#).

2.  **Create a Kubernetes cluster** 

    Select a cluster type based on your needs. For more information, see [Create a Kubernetes cluster](../../../../intl.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#), [Create a managed Kubernetes cluster](../../../../intl.en-US/User Guide/Kubernetes cluster/Cluster management/Create a managed Kubernetes cluster.md#), and [Create a serverless Kubernetes cluster](../../../../intl.en-US/User Guide/Serverless Kubernetes cluster/Cluster management/Create a serverless Kubernetes cluster.md#).

3.  **Use an image or orchestration template to create an application** 

    Use an existing image or orchestration template, or create an image or orchestration template to create an application. For more information, see [Create a deployment application by using an image](../../../../intl.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#) and [Create a Linux application by using an orchestration template](../../../../intl.en-US/User Guide/Kubernetes cluster/Application management/Create a Linux application by using an orchestration template.md#).

    **Note:** If you want to create an application that contains services supported by multiple images, we recommend that you use an orchestration template to create the application.

4.  **View the application status, and the services and pods of the application** 

    For more information, see [View a service](../../../../intl.en-US/User Guide/Kubernetes cluster/Application management/View a service.md#) and [View pods](../../../../intl.en-US/User Guide/Kubernetes cluster/Application management/View pods.md#).


