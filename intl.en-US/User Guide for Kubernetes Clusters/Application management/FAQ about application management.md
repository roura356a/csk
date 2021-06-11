# FAQ about application management

This topic provides answers to some frequently asked questions about application management.

-   [Troubleshoot application errors in Container Service for Kubernetes](~~211618~~)
-   [How do I manually upgrade Helm?](#section_42y_vhz_d9t)
-   [How do I use private images in Kubernetes clusters?](#section_b2s_ldm_84z)
-   [Precheck failure during a Cloud Controller Manager \(CCM\) upgrade](~~164988~~)
-   [How do I create containers from private images in an ACK cluster?](~~86562~~)
-   [Troubleshoot failures to bind source code in Container Registry](~~185631~~)
-   [Troubleshoot failures to create repositories in Container Registry](~~186529~~)

## How do I manually upgrade Helm?

1.  Log on to a master node in the Kubernetes cluster. For more information, see [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

2.  Run the following command.

    For the image address, enter the domain name of the image in the region where the virtual private cloud \(VPC\) that hosts the image is deployed. For example, if your server is deployed in the China \(Hangzhou\) region, the image address is registry-vpc.cn-hangzhou.aliyuncs.com/acs/tiller:v2.11.0.

    ```
    helm init --tiller-image registry.cn-hangzhou.aliyuncs.com/acs/tiller:v2.11.0 --upgrade
    ```

3.  After the tiller health check succeeds, you can run the `helm version` command to view the upgrade results.

    **Note:**

    -   The preceding command only upgrades Tiller, the server-side component of Helm. To upgrade the client-side component, download the required client binary.
    -   Download the latest client version [Helm client 2.11.0](https://github.com/helm/helm/releases/tag/v2.11.0) that is supported by Alibaba Cloud.
4.  After the client-side and server-side components of Helm are both upgraded, run the helm version command to view the following information:

    ```
    helm version
    ```

    ```
    Client: &version.Version{SemVer:"v2.11.0", GitCommit:"2e55dbe1fdb5fdb96b75ff144a339489417b146b", GitTreeState:"clean"}
    Server: &version.Version{SemVer:"v2.11.0", GitCommit:"2e55dbe1fdb5fdb96b75ff144a339489417b146b", GitTreeState:"clean"}
    ```


## How do I use private images in Kubernetes clusters?

1.  Run the following command:

    ```
    kubectl create secret docker-registry regsecret --docker-server=registry-internal.cn-hangzhou.aliyuncs.com --docker-username=abc@aliyun.com --docker-password=xxxxxx --docker-email=abc@aliyun.com
    ```

    **Note:**

    -   `regsecret`: the name of the Secret. You can enter a custom name.
    -   `--docker-server`: the address of the Docker registry.
    -   `--docker-username`: the username of the Docker registry.
    -   `--docker-password`: the logon password of the Docker registry.
    -   `--docker-email`: the email address. This parameter is optional.
    You can use the private image by using one of the following two methods:

    -   Manually configure the private image

        Add the Secret configuration to the YAML configuration file.

        ```
        containers:
            - name: foo
              image: registry-internal.cn-hangzhou.aliyuncs.com/abc/test:1.0
        imagePullSecrets:
            - name: regsecret
        ```

        **Note:**

        -   `imagePullSecrets` specifies the Secret that is required to pull the image.
        -   `regsecret` must be the same as the previous configured Secret name.
        -   The Docker registry address in `image` must be the same as the one that is specified in `--docker-server`.
        For more information, see [Use a private registry](https://kubernetes.io/docs/concepts/containers/images/#using-a-private-registry).

    -   Automatically configure the private image without the Secret

        **Note:** To avoid referencing the Secret each time you use private images for deployment, you can add the Secret configuration to the default service account of the namespace. For more information, see [Add ImagePullSecrets to a service account](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account).

        1.  Run the following command to view the Secret that is required to pull the private image:

            ```
            kubectl get secret regsecret
            ```

            ```
            NAME        TYPE                             DATA      AGE
            regsecret   kubernetes.io/dockerconfigjson   1         13m
            ```

            In this example, the default service account of the namespace is manually configured to use this Secret as the imagePullSecret.

        2.  Create an sa.yaml file and add the configuration of the default service account to this file.

            ```
            kubectl get serviceaccounts default -o yaml > ./sa.yaml
            
            cat  sa.yaml
            
            apiVersion: v1
            kind: ServiceAccount
            metadata:
              creationTimestamp: 2015-08-07T22:02:39Z
              name: default
              namespace: default
              resourceVersion: "243024"             ## Take note of the selfLink field: /api/v1/namespaces/default/serviceaccounts/default
              uid: 052fb0f4-3d50-11e5-b066-42010af0d7b6
            secrets:
            - name: default-token-uudgeoken-uudge
            ```

        3.  In the command-line interface \(CLI\), enter `vim sa.yaml` to open the sa.yaml file, delete the resourceVersion parameter, and then add the imagePullSecrets parameter to specify the Secret for pulling images. The following sample code shows the modification:

            ```
            apiVersion: v1
            kind: ServiceAccount
            metadata:
              creationTimestamp: 2015-08-07T22:02:39Z
              name: default
              namespace: default
              selfLink: /api/v1/namespaces/default/serviceaccounts/default
              uid: 052fb0f4-3d50-11e5-b066-42010af0d7b6
            secrets:
            - name: default-token-uudge
            imagePullSecrets:                 ## This field is newly added.
            - name: regsecret                                    
            ```

        4.  Use the configuration in the sa.yaml file to replace the configuration of the default service account.

            ```
            kubectl replace serviceaccount default -f ./sa.yaml
            serviceaccount "default" replaced
            ```

        5.  In the CLI, enter kubectl create -f to create a Tomcat application.

            ```
            apiVersion: apps/v1 
            kind: Deployment
            metadata:
              name: tomcat-deployment
              labels:
                app: tomcat
            spec:
              replicas: 1
              selector:
                matchLabels:
                  app: tomcat
              template:
                metadata:
                  labels:
                    app: tomcat
                spec:
                  containers:
                  - name: tomcat
                    image: registry-internal.cn-hangzhou.aliyuncs.com/abc/test:1.0              # Replace the value with the address of your private image
                    - containerPort: 8080
            ```

        6.  If the configuration is correct, the pod is started. In the CLI, enter kubectl get pod tomcat-xxx -o yaml. You can find the following configuration in the command output:

            ```
            spec:
              imagePullSecrets:
            - nameregsecretey
            ```


