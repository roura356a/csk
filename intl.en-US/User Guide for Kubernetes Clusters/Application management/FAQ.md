# FAQ

This topic provides answers to some commonly asked questions about application management.

-   [How do I manually upgrade Helm?](#section_42y_vhz_d9t)
-   [How do I use private images in Kubernetes clusters?](#section_b2s_ldm_84z)

## How do I manually upgrade Helm?

1.  Log on to a master node in an Alibaba Cloud Container Service for Kubernetes \(ACK\) cluster. For more information, see [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to a cluster.md).

2.  Run the following command.

    For the image address, enter the address of the image in the Virtual Private Cloud \(VPC\) network in the region. For example, if your server is deployed in the China \(Hangzhou\) region, the image address is registry-vpc.cn-hangzhou.aliyuncs.com/acs/tiller:v2.11.0.

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

    -   `regsecret`: the name of the secret. You can enter a custom name.
    -   `--docker-server`: the address of the Docker registry.
    -   `--docker-username`: the username of the Docker registry.
    -   `--docker-password`: the logon password of the Docker registry.
    -   `--docker-email`: the email address. This parameter is optional.
    You can perform the following operations.

    -   Manually configure the private image

        Add the secret to the YAML configuration file.

        ```
        containers:
            - name: foo
              image: registry-internal.cn-hangzhou.aliyuncs.com/abc/test:1.0
        imagePullSecrets:
            - name: regsecret
        ```

        **Note:**

        -   `imagePullSecrets` specifies the secret that is required to pull the image.
        -   `regsecret` must be the same as the previous configured secret name.
        -   The Docker registry address in `image` must be the same as the one that is specified in `--docker-server`.
        For more information, see [Use a private registry](https://kubernetes.io/docs/concepts/containers/images/#using-a-private-registry).

    -   Implement an orchestration without the secret

        **Note:** To avoid referencing the secret each time you use private images for deployment, you can add the secret to the default service account of the namespace. For more information, see [Add ImagePullSecrets to a service account](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account).

        1.  Run the following command to view the secret that is required to pull private images.

            ```
            kubectl get secret regsecret
            ```

            ```
            NAME        TYPE                             DATA      AGE
            regsecret   kubernetes.io/dockerconfigjson   1         13m
            ```

            In this example, the default service account of the namespace is manually configured to use this secret as the imagePullSecret.

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
              resourceVersion: "243024"             ##Note this parameter selfLink: /api/v1/namespaces/default/serviceaccounts/default
              uid: 052fb0f4-3d50-11e5-b066-42010af0d7b6
            secrets:
            - name: default-token-uudgeoken-uudge
            ```

        3.  In the command-line interface \(CLI\), enter `vim sa.yaml` to open the sa.yaml file, delete the resourceVersion parameter, and then add the imagePullSecrets parameter to specify the secret for pulling images. The following sample code shows the modification:

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
            imagePullSecrets:                 ## New parameter
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

        6.  If the configuration is valid, the pod is started. In the CLI, enter kubectl get pod tomcat-xxx -o yaml. You can find the following configuration in the command output:

            ```
            spec:
              imagePullSecrets:
            - nameregsecretey
            ```


