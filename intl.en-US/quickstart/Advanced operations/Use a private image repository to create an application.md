# Use a private image repository to create an application {#concept_jfx_d2t_42b .concept}

In many scenarios, an image in a private image repository is used for deploying an application. In this document, use Alibaba Cloud image repository service to create a private image repository, and create an application that uses this private image repository.

## Step 1 Create a private image repository {#section_xkb_f2t_42b .section}

1.  Log on to the [Container Registry console](https://cr.console.aliyun.com/).
2.  Click **Repositories** in the left-side navigation pane, select the target region, and lick **Create Repository**.
3.  Configure the image repository in the dialog box, and then click **Create Repository**. In this example, select the private image repository type and set the code source as a local repository.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16441/15389634217483_en-US.png)

4.  On the repositories page, select the target region, and you can see that the created image repository. Click **Manage**on the right.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16441/15389634217484_en-US.png)

5.  On the repository management page, click **Details**, and you can follow the guide to use the private image repository.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16441/15389634217485_en-US.png)

6.  Log on to the image repository in the Linux environment and upload the local image to the private image repository.

    ```
    $ sudo docker login --username=abc@aliyun.com
     Password                                                                        ## Image repository independent login password:
     Login Succe ed
     
    $ dockeagesr im                                                                 #This example is tomcat ages
    REPOSITORY                                                                 TAG                 IMAGE ID            CREATED             SIZE
    tomcat                                                                     latest              2d43521f2b1a        6 days ago          463MB
    
     $ sudo docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/kubernetes-java/tomcat-private:[Image version number]            #V1 in this example
     $ sudo docker push registry.cn-hangzhou.aliyuncs.com/kubernetes-java/tomcat-private:[Inage version number]                       #V1 in this example           
     
     The push refers to a repository [registry.cn-hangzhou.aliyuncs.com/kubernetes-java/tomcat-private]
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
    V1: digest: sha256:cded14cf64697961078aedfdf870e704a52270188c8194b6f70c778a8289d87e size: 2836
    ```

7.  Return to the image repository detail page, and click **Image version** in the left navigation pane, you can see that the image has been uploaded successfully, and you can view the image version information.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16441/15389634217489_en-US.png)


## Step 2 Create a docker-registry secret {#section_flb_f2t_42b .section}

When using Kubernetes to create an application by pulling a private image, pass the identity authentication information of the private image repository to Kubernetes through a docker-registry secret.

Create a docker-registry secret as follows:

```
kubectl create secret docker-registry regsecret --docker-server=registry-internal.cn-hangzhou.aliyuncs.com --docker-username=abc@aliyun.com --docker-password=xxxxxx --docker-email=abc@aliyun.com
```

where:

-   --regsecret: Specifies the secret key name and the name is customizable.
-   --docker-server: Specifies the Docker repository address.
-   --docker-username: Specifies the user name of the Docker repository.
-   --docker-password: Specifies the Docker repository login password, namely, the independent login password of the container image registry.
-   --docker-email: Specifies the email address.

**Note:** You cannot use the secrets on the Container Service console to create secrets.

To pull an image successfully, add the secret parameter to the yml file.

```
containers:
    - name: foo
      image: registry-internal.cn-hangzhou.aliyuncs.com/abc/test:1.0
imagePullSecrets:
    - name: regsecret
```

where:

-   imagePullSecrets declares that a secret key must be specified when you pull the image.
-   regsecret must be the same as the preceding secret key name.
-   The docker repository name in the image must be the same as that in the -- docker-server.

## Step 3 Use a private image repository to create an application {#section_mlb_f2t_42b .section}

The orchestration is as follows:

```
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

For more information, see the official Kubernetes documentation [Use a Private Registry](https://kubernetes.io/docs/concepts/containers/images/?spm=a2c4g.11186623.2.1.XVyfik#using-a-private-registry).

