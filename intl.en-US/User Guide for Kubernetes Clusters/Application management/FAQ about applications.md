---
keyword: [application management, frequently asked questions, FAQ]
---

# FAQ about applications

This topic provides answers to some frequently asked questions about application management in Kubernetes clusters.

-   [Troubleshoot application errors in Container Service for Kubernetes \(ACK\)](~~211618~~)
-   [How do I manually upgrade Helm?](#section_42y_vhz_d9t)
-   [How do I use private images in Kubernetes clusters?](#section_b2s_ldm_84z)
-   [Precheck failure during a Cloud Controller Manager \(CCM\) upgrade](~~164988~~)
-   [How do I create containers from private images in an ACK cluster?](~~86562~~)
-   [Troubleshoot failures to bind source code in Container Registry](~~185631~~)
-   [Troubleshoot repository creation failures in Container Registry](~~186529~~)

## How do I manually upgrade Helm?

1.  Log on to your Kubernetes cluster. For more information, see [Connect to ACK clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

2.  Run the following command to install Tiller.

    For the image address, enter the domain name of the image in the region where the virtual private cloud \(VPC\) that hosts the image is deployed. For example, if your machine is deployed in the China \(Hangzhou\) region, the image address is `registry-vpc.cn-hangzhou.aliyuncs.com/acs/tiller:v2.11.0`.

    ```
    helm init --tiller-image registry.cn-hangzhou.aliyuncs.com/acs/tiller:v2.11.0 --upgrade
    ```

3.  After the tiller health check succeeds, you can run the `helm version` command to view the upgrade results.

    **Note:**

    -   The preceding command only upgrades Tiller, the server-side component of Helm. To upgrade the client-side component, download the required client binary.
    -   Download the latest client version [Helm client 2.11.0](https://github.com/helm/helm/releases/tag/v2.11.0) that is supported by Alibaba Cloud.
4.  After the server-side component and client-side component of Helm are upgraded, run the following command to check their versions:

    ```
    helm version
    ```

    Expected output:

    ```
    Client: &version.Version{SemVer:"v2.11.0", GitCommit:"2e55dbe1fdb5fdb96b75ff144a339489417b****", GitTreeState:"clean"}
    Server: &version.Version{SemVer:"v2.11.0", GitCommit:"2e55dbe1fdb5fdb96b75ff144a339489417b****", GitTreeState:"clean"}
    ```


## How do I use private images in Kubernetes clusters?

1.  Run the following command to create a Secret:

    ```
    kubectl create secret docker-registry regsecret --docker-server=registry-internal.cn-hangzhou.aliyuncs.com --docker-username=abc****@aliyun.com --docker-password=**** --docker-email=abc****@aliyun.com
    ```

    **Note:**

    -   `regsecret`: the key of the Secret. You can enter a custom key.
    -   `--docker-server`: the address of the Docker registry.
    -   `--docker-username`: the username of the Docker registry.
    -   `--docker-password`: the logon password of the Docker registry.
    -   `--docker-email`: the email address.
    You can schedule a pod to the virtual node in one of the following ways:

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

        **Note:** The Secret for pulling images is required each time you pull a private image. To prevent repetitively referencing the Secret, you can add the Secret to the default service account of the namespace that you use. For more information, see [Add ImagePullSecrets to a service account](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account).

        1.  Run the following command to query the Secret that is required to pull the private image:

            ```
            kubectl get secret regsecret
            ```

            Expected output:

            ```
            NAME        TYPE                             DATA      AGE
            regsecret   kubernetes.io/dockerconfigjson   1         13m
            ```

            In this example, the **default** service account of the namespace is manually configured to use this Secret as the imagePullSecret.

        2.  Create an sa.yaml file and add the configuration of the default service account to this file.

            ```
            kubectl get serviceaccounts default -o yaml > ./sa.yaml
            ```

        3.  Run the following command to query the content of the sa.yaml file:

            ```
            cat  sa.yaml
            ```

            Expected output:

            ```
            apiVersion: v1
            kind: ServiceAccount
            metadata:
              creationTimestamp: 2015-08-07T22:02:39Z
              name: default
              namespace: default
              resourceVersion: "243024"             # Take note of the selfLink field: /api/v1/namespaces/default/serviceaccounts/default. 
              uid: 052fb0f4-3d50-11e5-b066-42010af0****
            secrets:
            - name: default-token-uudgeoken-uudge
            ```

        4.  In the CLI, run the `vim sa.yaml` command to open the sa.yaml file. Delete the resourceVersion parameter and add the imagePullSecrets parameter to specify the Secret for pulling images. Modify the file based on the following content:

            ```
            apiVersion: v1
            kind: ServiceAccount
            metadata:
              creationTimestamp: 2015-08-07T22:02:39Z
              name: default
              namespace: default
              selfLink: /api/v1/namespaces/default/serviceaccounts/default
              uid: 052fb0f4-3d50-11e5-b066-42010af0****
            secrets:
            - name: default-token-uudge
            imagePullSecrets:                 # Add this parameter. 
            - name: regsecret                                    
            ```

        5.  Run the following command to replace the configuration of the default service account with the configuration in the sa.yaml file:

            ```
            kubectl replace serviceaccount default -f ./sa.yaml
            ```

            Expected output:

            ```
            serviceaccount "default" replaced
            ```

        6.  Create a Tomcat application.

            Use the following template to create a tomcat.yaml file:

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
                    image: registry-internal.cn-hangzhou.aliyuncs.com/abc/test:1.0              # Replace the value with the address of your private image. 
                    - containerPort: 8080
            ```

            Run the following command to create a Tomcat application:

            ```
            kubectl create -f tomcat.yaml
            ```

        7.  After the pod is started, run the following command to query the pod configurations:

            ```
            kubectl get pod tomcat-**** -o yaml
            ```

            Expected output:

            ```
            spec:
              imagePullSecrets:
            - nameregsecretey
            ```


