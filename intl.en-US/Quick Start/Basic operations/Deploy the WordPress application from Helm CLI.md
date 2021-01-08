---
keyword: [Helm CLI, WordPress]
---

# Deploy the WordPress application from Helm CLI

This topic describes how to deploy the WordPress application in a cluster of Container Service for Kubernetes \(ACK\) from Helm command-line interface \(CLI\).

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Install and set up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## Install and set up Alibaba Cloud CLI and kubectl

-   Install Helm. You can install Helm in the following ways:
    -   Use the source code or a binary package to install Helm. For more information, see [Installing Helm](https://helm.sh/docs/intro/install/).
    -   Run the following script to install Helm:

        ```
        curl -sSL https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
        ```

    -   Use a package manager to install Helm.
        -   Install Helm in macOS. For more information, see [Homebrew](https://brew.sh/). After the packet manager is installed, run the following command to install Helm:

            ```
            brew install helm
            ```

        -   Install Helm in Windows. For more information, see [Chocolatey](https://chocolatey.org/). After the packet manager is installed, run the following command to install Helm:

            ```
            choco install kubernetes-helm
            ```

-   Create a Helm repository. Alibaba Cloud provides Helm repository images for developers in China. This provides an easy way to use Helm.
    -   Run the following command to create a stable repository from an image that is provided by Alibaba Cloud:

        ```
        helm repo add stable https://apphub.aliyuncs.com/stable/
        ```

        **Note:** Users outside China can use official Helm repository images. Run the following command to create a stable repository from an official image:

        ```
        helm repo add stable https://kubernetes-charts.storage.googleapis.com/
        ```

    -   Run the following command to search for the stable repository.

        ```
        helm search repo stable
        ```

-   Install and set up the kubectl client. For more information, see [Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## Deploy WordPress

1.  Run the following command to add an official Helm repository:

    ```
    helm repo add bitnami https://charts.bitnami.com/bitnami
    ```

2.  Run the following command to deploy WordPress:

    ```
    helm install wordpress bitnami/wordpress \
        --set mariadb.master.persistence.enabled=true \
        --set mariadb.master.persistence.storageClass=alicloud-disk-ssd \
        --set mariadb.master.persistence.size=20Gi \
        --set persistence.enabled=false                        
    ```

    **Note:**

    -   MariaDB persists data to persistent volumes \(PVs\).
    -   You can provision an SSD of 20 GiB as the PV. The PV can be claimed by setting StorageClassName.
    -   If you do not want WordPress to persist data, you can set persistence.enabled to false.
    If information similar to the following output is returned, it indicates that the WordPress application is deployed:

    ```
    NAME: wordpress
    LAST DEPLOYED: Tue Sep  8 10:37:05 2020
    NAMESPACE: default
    STATUS: deployed
    REVISION: 1
    NOTES:
    ** Please be patient while the chart is being deployed **
    
    Your WordPress site can be accessed through the following DNS name from within your cluster:
    
        wordpress.default.svc.cluster.local (port 80)
    
    To access your WordPress site from outside the cluster follow the steps below:
    
    1. Get the WordPress URL by running these commands:
    
      NOTE: It may take a few minutes for the LoadBalancer IP to be available.
            Watch the status with: 'kubectl get svc --namespace default -w wordpress'
    
       export SERVICE_IP=$(kubectl get svc --namespace default wordpress --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
       echo "WordPress URL: http://$SERVICE_IP/"
       echo "WordPress Admin URL: http://$SERVICE_IP/admin"
    
    2. Open a browser and access WordPress using the obtained URL.
    
    3. Login with the following credentials below to see your blog:
    
      echo Username: user
      echo Password: $(kubectl get secret --namespace default wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode)
                            
    ```


## Access the WordPress application

1.  After Helm is installed, follow the instructions and run the following command to query the IP address that is used to access the application:

    ```
    kubectl get svc --namespace default wordpress-acs-sample --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}"
    ```

    Sample output:

    ```
    192.168.171.110  
    ```

2.  Run the following command to query the password of the admin account:

    ```
    kubectl get secret --namespace default wordpress-ack-wordpress-sample -o jsonpath="{.data.wordpress-password}" | base64 --decode
    K*****X**7
    ```

3.  Enter the IP address into the address bar of your browser and press Enter to access the WordPress application. On the logon page, enter admin as the username and enter the obtained password to log on to the application.

    ![36](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3635229061/p200143.png)


## Delete the WordPress application

Run the following command to delete the WordPress application:

```
helm delete wordpress
release "wordpress" uninstalled
```

