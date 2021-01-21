# Create an application from a private image repository

In many scenarios, you use an image in a private image repository to deploy an application. This topic describes how to create a private image repository in the Container Registry console and use an image in this repository to create an application.

## Create a private image repository

If this is the first time you use the Container Registry console, the Tips message appears, prompting you to set a password for logging on to the console. Click **Activate Now** and set a password.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **Alibaba Cloud Container Registry**.

3.  In the left-side navigation pane of the Container Registry console, choose **Default Instance** \> **Repositories**. In the top menu bar, select a region to deploy the repository and click **Create Repository**.

4.  In the Create Repository dialog box, set the **Namespace**, **Repository Name**, **Summary**, and **Repository Type** parameters. In this example, the type of the repository is private. Click **Next**.

    ![Create an image repository](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8441258951/p7484.png)

5.  On the Code Source wizard page, set Code Source to **Local Repository** and click **Create Repository**.

6.  On the Repositories page, select the region and namespace where the repository is created, find the created repository, and click **Manage** in the Actions column.

7.  On the **Details** page of the repository, click the **Guide** tab and view details about how to use the private image repository.

    ![Repository details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8441258951/p11349.png)

8.  Log on to the repository from a Linux server and run the following command to upload a local image to the repository:

    ```
    sudo docker login --username=abc@aliyun.com registry.cn-hangzhou.aliyuncs.com
    Password:   ## The password that is used to log on to the repository.                                                                              
    Login Succeeded
    ```

    ```
    docker images            
    # Tomcat is used as an example.
           REPOSITORY       TAG            IMAGE ID            CREATED            SIZE
           tomcat          latest         2d43521f2b1a        6 days ago          463MB
    ```

    ```
    sudo docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/the name of the namespace/tomcat-private:[image version]           
    sudo docker push registry.cn-hangzhou.aliyuncs.com/the name of the namespace/tomcat-private:[image version]
    ```

    After the private image is created, the system prints the following output:

    ```
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

9.  Go to the Details page of the repository. In the left-side navigation pane, choose **Tags** and verify that the local image is uploaded to the repository. You can also view the image version.

    ![Tags](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8441258951/p11362.png)


## Create a private repository Secret

To pull private images, you must use a **private repository Secret**.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.

4.  In the left-side navigation pane, click **Configurations**.

5.  On the **Configurations** page, click the Secrets tab.

6.  In the upper-right corner of the Secrets tab, click **Create**.

7.  In the **Create** panel, set the parameters and click **OK**.

    ![Configure the Secret](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8441258951/p42130.png)

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the Secret.|
    |Type|The following types of Secret are supported:    -   Opaque: a general Secret. Enter a key and a value. The value must be encoded in Base64.
    -   Private Repository Logon Secret: the credentials that are required to pull images from a private image repository. Enter the address, username, and password of the repository.

**Note:** The username is the full name of your Alibaba Cloud account. The password is the one that is specified to log on to the Container Registry console. You can go to the **Access Credential** page to change the password.

    -   TLS Certificate: Transport Layer Security \(TLS\) certificates are used to verify user identities.
        -   Cert: Enter the content of the TLS certificate.
        -   Key: Enter the private key of the TLS certificate. |

    After the Secret is created, you are redirected to the Secrets page. You can find the newly created Secret in the list.

    ![Secret list](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8441258951/p42134.png)


**Note:**

You can also create a **private repository Secret** from the command-line interface \(CLI\). For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

## Create an application from the private image repository

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.

4.  In the left-side navigation pane, click **Workloads**.

5.  On the **Workload** page, click the **Deployments** tab.

6.  In the upper-right corner of the **Deployments** tab, click **Create from Template**.

    **Note:** You can also click **Create from Image** to create an application. For more information, see [Use an image secret](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image secret.md).

7.  Set **Sample Template** to **Custom** and copy the following content to **Template**.

    ```
    apiVersion: apps/v1 
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
            image: registry.cn-hangzhou.aliyuncs.com/the name of the namespace/tomcat-private:latest
            ports:
            - containerPort: 8080
          imagePullSecrets:
          - name: regsecret
    ```

8.  Click **Create**.

    Go to the Deployments page. You can view the newly created application.

    ![private-image](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8441258951/p132607.png)


For more information, see [Use a private image repository](https://kubernetes.io/docs/concepts/containers/images/?spm=a2c4g.11186623.2.1.XVyfik#using-a-private-registry).

