---
keyword: [Helm CLI, WordPress]
---

# 通过Helm CLI一键部署WordPress

本文介绍如何通过Helm CLI部署WordPress应用。

## 前提条件

-   已创建一个ACK集群。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已通过Kubectl连接到Kubernetes托管版集群。具体操作，请参见[通过Kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

## 安装配置阿里云CLI和kubectl

-   安装Helm。您可以选择以下三种方式：
    -   通过源码或二进制方式安装，请参见[Installing Helm](https://helm.sh/docs/intro/install/)。
    -   通过执行以下脚本安装。

        ```
        curl -sSL https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
        ```

    -   通过包管理器工具安装。
        -   OS X安装：请参见[Homebrew](https://brew.sh/)，安装包管理工具后，执行以下命令安装Helm。

            ```
            brew install helm
            ```

        -   Windows 安装：请参考[Chocolatey](https://chocolatey.org/)，安装包管理工具后，执行以下命令安装Helm。

            ```
            choco install kubernetes-helm
            ```

-   配置Helm Repo。阿里云为中国的开发者提供了Helm Repo的镜像，开发者可以更加方便地使用Helm。
    -   执行以下命令配置阿里云发布的稳定版Repo。

        ```
        helm repo add stable https://apphub.aliyuncs.com/stable/
        ```

        **说明：** 如果您是海外用户，可以继续使用官方版Repo。执行以下命令进行配置。

        ```
        helm repo add stable https://kubernetes-charts.storage.googleapis.com/
        ```

    -   执行以下命令搜索Repo。

        ```
        helm search repo stable
        ```

-   安装和设置kubectl客户端，详情请参见[Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

## 部署WordPress

1.  执行以下命令，添加WordPress官方Repo地址。

    ```
    helm repo add bitnami https://charts.bitnami.com/bitnami
    ```

2.  执行以下命令部署WordPress。

    ```
    helm install wordpress bitnami/wordpress \
        --set mariadb.master.persistence.enabled=true \
        --set mariadb.master.persistence.storageClass=alicloud-disk-ssd \
        --set mariadb.master.persistence.size=20Gi \
        --set persistence.enabled=false                        
    ```

    **说明：**

    -   为了实现数据的持久化，mariadb使用持久化存储卷保存数据。
    -   持久化存储选用阿里云SSD云盘，大小为20 GiB，并通过配置StorageClassName自动化创建。
    -   WordPress不需要进行数据持久化，数据卷persistence.enabled可配置为false。
    系统输出类似以下结果，表示已经部署完成。

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


## 访问WordPress

1.  安装Helm成功后，根据部署提示，执行以下命令获取访问IP地址。

    ```
    kubectl get svc --namespace default wordpress-acs-sample --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}"
    ```

    系统输出类似以下结果：

    ```
    192.168.171.110  
    ```

2.  执行以下命令获取admin访问密码。

    ```
    kubectl get secret --namespace default wordpress-ack-wordpress-sample -o jsonpath="{.data.wordpress-password}" | base64 --decode
    K*****X**7
    ```

3.  在地址栏中输入IP地址访问WordPress应用，然后使用用户名admin和访问密码登录admin界面。

    ![36](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4780208061/p200143.png)


## 删除应用

您可以执行以下命令删除部署的WordPress。

```
helm delete wordpress
release "wordpress" uninstalled
```

