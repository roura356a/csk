---
keyword: [create a Jupyter notebook, use a Jupyter notebook]
---

# Create and use a Jupyter notebook

The cloud-native AI console provides a platform for data scientists to perform machine learning tasks, such as data inspection, data preprocessing, model building, model analysis, model testing, and model publishing. Jupyter Notebook is a development utility for machine learning processes. Container Service for Kubernetes \(ACK\) integrates Jupyter Notebook with the AI console and the development environment. ACK also provides an authorization mechanism for you to manage Jupyter Notebook. This topic describes how to create and use a Jupyter notebook in the AI development console.

-   [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).
-   The **ack-ai-dev-console** and **ack-ai-installer** of the cloud-native AI component set are installed in the professional Kubernetes cluster. The cluster must run Kubernetes 1.18 or later.
-   A Resource Access Management \(RAM\) user is created by the cluster administrator. Required quota groups are added and associated with the RAM user. For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Basic operations/Create a RAM user.md) and [Step 1: Add a quota group and associate the quota group with the RAM user](/intl.en-US/Cloud-native AI User Guide/AI console usage guide/Development/Log on to the AI development console.md).
-   Training data is prepared. For more information, see [Configure datasets and source code repositories for a training job](/intl.en-US/Cloud-native AI User Guide/AI console usage guide/Development/Configure datasets and source code repositories for a training job.md).
-   Persistent volume claims \(PVCs\) are created. For more information, see [Use a NAS file system as a statically provisioned volume in the ACK console](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/Use a statically provisioned NAS volume.md) or [Mount an OSS bucket as a statically provisioned volume in the ACK console](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/OSS volumes/Mount a statically provisioned OSS volume.md).

    **Note:** In most cases, data used to train models is stored in Object Storage Service \(OSS\) volumes or Apsara File Storage NAS \(NAS\) volumes.


## Introduction to Jupyter Notebook

-   Provides a machine learning experiment environment that can be integrated into a cloud platform. This environment allows you to develop machine learning on your on-premises machine.
-   Provides a tool to copy and process datasets. This allows you to submit processed data to persistent storage. You can also process datasets by using big data tools.
-   Provides an environment for machine learning processes, such as testing and preprocessing. In the environment, you can write the code of machine learning tasks, package the code into `Docker images`, and push the images to your cluster or remote Docker repositories.

## Step 1: Create a Jupyter notebook

1.  Log on to the AI development console. For more information, see [Log on to the AI development console](/intl.en-US/Cloud-native AI User Guide/AI console usage guide/Development/Log on to the AI development console.md).

2.  In the left-side navigation pane of the AI development console, click **Notebook**.

3.  On the **Notebook** page, click **create notebook**.

4.  In the **Notebook message** and **Notebook configure** sections, set the parameters and click **create notebook**.

    ![2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4318909261/p298739.png)

    1.  Set **Notebook Name**, **Notebook Image**, **Namespace**, and **Data Configuration**. The selected namespace must be the one that the cluster administrator creates for AI development. You can set the other parameters based on your requirements.

    2.  Turn on **Workspace PVC**. Select the persistent volume claim \(PVC\) associated to the workspace from the **Target PVC** drop-down list.

    3.  In the **Notebook configure** section, set **CPU \(Cores\)**, **Memory \(GB\)**, and **GPU \(Card Numbers\)**.

5.  After the notebook is created, go to the **Notebook** page and click the created notebook to view the detailed information.


## Step 2: Use a Jupyter notebook

1.  On the **Notebook** page, find a notebook in the **Running** **state** and click its name.

2.  On the page that appears, click **Python 3** in the **Notebook** section.

    **Note:** On the create notebook page, the notebook images provided by ACK supports only Python. If you want to use other languages, you can create and package custom notebook images by using Dockerfiles. For more information, see [Create a custom notebook image](#section_pvq_304_w7w).

    ![23](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4318909261/p298900.png)

3.  Use Arena to submit a task in the notebook.

    Method 1: Use Arena CLI to submit a task

    ![24](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0288909261/p298902.png)

    Method 2: Use Arena SDK for Python to submit a task

    ![25](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0288909261/p298903.png)


## Create a custom notebook image

To meet various requirements of different users, ACK allows you to specify a custom notebook image, as shown in the following figure.

![28](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5318909261/p299392.png)

You can perform the following steps to create and package a custom image by using a Dockerfile:

```
FROM tensorflow/tensorflow:1.15.5-gpu
USER root
RUN pip install jupyter && \
    pip install ipywidgets && \
    jupyter nbextension enable --py widgetsnbextension && \
    pip install jupyterlab && jupyter serverextension enable --py jupyterlab
# You can use other methods to install JupyterLab. You must expose the service through port 8888.
EXPOSE 8888
USER jovyan
CMD ["sh", "-c", "jupyter-lab --notebook-dir=/home/jovyan --ip=0.0.0.0 --no-browser --allow-root --port=8888 --ServerApp.token='' --ServerApp.password='' --ServerApp.allow_origin='*' --ServerApp.base_url=${NB_PREFIX} --ServerApp.authenticate_prometheus=False"]
USER root
```

The following table describes the parameters.

|Parameter|Description|
|---------|-----------|
|--notebook-dir|The working directory to be started. Default value: `/home/jovyan`. This is the JupyterLab official setting.|
|--ip|The IP address that the Jupyter service listens on. Default value: `0.0.0.0`. This allows access from external IP addresses.|
|--no-browser|Specifies that no browser is used. This is supported only by the Linux server environment.|
|--port|The port that is listened on for external requests. This parameter is required and must be set to `8888`. This is the JupyterLab official setting.|
|--ServerApp.token|The custom logon `token`. By default, this parameter is empty. If you set this parameter, you must notify the users.|
|--ServerApp.password|The custom logon password. By default, this parameter is an empty string. If you set this parameter, you must notify the users.|
|--ServerApp.base\_url|The path from which the JupyterLab service starts. The path is required for routing. Therefore, the value is derived from the `NB_PREFIX` environment variable. This parameter is required and must be set to `${NB_PREFIX}`.|
|--ServerApp.allow\_origin|The source IP group that can be accessed by JupyterLab. Default value: `'*'`. This indicates that all source IP groups can be accessed by JupyterLab.|
|--ServerApp.authenticate\_prometheus|Specifies whether to enable authentication when Jupyter accesses Prometheus metrics. Default value: `False`. This indicates that the authentication is disabled. You can enable the authentication based on your business requirements.|

