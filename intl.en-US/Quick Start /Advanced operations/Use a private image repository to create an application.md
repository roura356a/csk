# Use a private image repository to create an application {#concept_jfx_d2t_42b .concept}

This topic describes how to create a private image repository in the Alibaba Cloud Container Registry console and how to use an image in this image repository to create an application in the Container Service console.

## Step 1: Create a private image repository {#section_xkb_f2t_42b .section}

**Note:** If you use Alibaba Cloud Container Registry, the system prompts you to set the logon password. You must click **auto enable** and then set the registry password.

1.  Log on to the [Container Registry console](https://partners-intl.console.aliyun.com/#/cr).
2.  In the left-side navigation pane, click **Repositories**. Select the target region, and then click **Create Repository**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16441/15646581447483_en-US.png)

3.  In the displayed dialog box, set the following parameters for the image repository: namespace, repository name, repository type, and summary. Then, click **Next**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16441/15646581447484_en-US.png)

4.  Select **Local Repository**, and then click **Create Repository**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16441/15646581447485_en-US.png)

5.  On the Repositories page, select the target region and namespace, find the image repository that you have created, and then click **Manage** in the **Actions** column.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16441/15646581457489_en-US.png)

6.  On the Details page, view how to use this private image repository.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16441/156465814511349_en-US.png)

7.  Log on to this image repository in the Linux operating system, and run the following commands to upload a local image to the private image repository.

    ``` {#codeblock_81d_pxl_g6s}
    $ sudo docker login --username=abc@aliyun.com
     Password                                                                                 ## The password for logging on to the image repository.
     Login Succeed
    
    $ docker images                                                                             #A Tomcat image is used.
    REPOSITORY                                                                 TAG                 IMAGE ID            CREATED             SIZE
    tomcat                                                                     latest              2d43521f2b1a        6 days ago          463MB
    
    $ sudo docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/XXX/tomcat-private:[image version number]           
    $ sudo docker push registry.cn-hangzhou.aliyuncs.com/XXX/tomcat-private:[image version number]
    ```

    The following is the output:

    ``` {#codeblock_3eu_o9b_plq}
    The push refers to a repository [registry.cn-hangzhou.aliyuncs.com/XXX/tomcat-private]
    9072c7b03a1b: Pushed
    f9701cf47c58: Pushed
    365c8156ff79: Pushed
    2de08d97c2ed: Pushed
    6b09c39b2b33: Pushed
    4172ffa172a6: Pushed
    1dccf0da88f3: Pushed
    d2070b14033b: Pushed
    63dcf81c7ca7: Pushed
    ce6466f43b11: Pushed
    719d45669b35: Pushed
    3b10514a95be: Pushed
    V1: digest: sha256:cded14cf64697961078aedfdf870e704a52270188c8194b6f70c778a8289**** size: 2836
    ```

8.  Return to the Details page of the image repository. Then, in the left navigation pane, click **Tags** to verify that the local image has been uploaded.

    **Note:** The information related to the image such as the image version number and image ID is displayed on the page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16441/156465814511362_en-US.png)


## Step 2: Create a secret for logging on to a private image repository {#section_0qr_sux_srg .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Configuration** \> **Secrets**.
4.  Select the target cluster and namespace, and then click **Create** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16441/156465814542123_en-US.png)

5.  Set a secret.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16441/156465814542130_en-US.png)

    Then, the newly created secret is displayed in the secret list.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16441/156465814642134_en-US.png)


You can also use the kubectl CLI to create a secret for logging on to a private image repository. For more information, see [Connect to a Kubernetes cluster by using kubectl](../../../../reseller.en-US//Connect to a Kubernetes cluster by using kubectl.md#).

## Step 3: Use a private image repository to create an application {#section_wun_qec_6za .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Deployments**.
4.  Select the target cluster and namespace, and then click **Create from Template**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16441/156465814642136_en-US.png)

    **Note:** You can also create an application by clicking **Create from Image**. For more information, see [Use an image Secret](../../../../reseller.en-US/User Guide for Kubernetes Clusters/Application management/Use an image Secret.md#).

5.  Select **Custom** from the **Sample Template** drop-down list, copy the following code and paste it to the **Template** area, and then click **DEPLOY**.

    ``` {#codeblock_22g_0ww_l88}
    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment 
    metadata: 
      name: private-image
      nameSpace: default  
      labels:  
        app: private-image  
    spec:   
      replicas: 1
      selector:
        matchLabels:
          app: private-image
      template:
        metadata:
          labels:
            app: private-image
        spec:
          containers:
          - name: private-image
            image: registry.cn-hangzhou.aliyuncs.com/xxx/tomcat-private:latest
            ports:
            - containerPort: 8080
          imagePullSecrets:
          - name: regsecret
    ```

    **Note:** You must replace the sample image URL in the preceding code with your private image URL.


For more information, see [Use a private repository](https://kubernetes.io/docs/concepts/containers/images/?spm=a2c4g.11186623.2.1.XVyfik#using-a-private-registry).

