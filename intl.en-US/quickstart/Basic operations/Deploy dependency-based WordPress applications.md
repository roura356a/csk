# Deploy dependency-based WordPress applications {#task_dd2_2td_n2b .task}

-   Create a Kubernetes cluster. For more information, see [Create a Kubernetes cluster quickly](reseller.en-US/quickstart/Basic operations/Create a Kubernetes cluster quickly.md#).
-   Create storage volumes and storage volume claims. For how to create a storage volume, see [Use Alibaba Cloud cloud disks](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage/Use Alibaba Cloud cloud disks.md#), [Use Alibaba Cloud NAS](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage/Use Alibaba Cloud NAS.md#), and [Use Alibaba Cloud OSS](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage/Use Alibaba Cloud OSS.md#). For how to create a storage volume claim, see [Create a persistent storage volume claim](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage claim management/Create a persistent storage volume claim.md#). Use Alibaba Cloud disks as storage volumes. In the example, choose PV/PVC for the storage volume mount. Create two storage volume claims: wordpress-pv-claim and wordpress-mysql-pv-claim which are used in the wordpress yaml file and the wordpress-mysql yaml file respectively, to mount corresponding storage volumes.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15530676547682_en-US.png)


This example shows how to create dependency-based applications by customizing a template in a orchestration template.

The main components are:

-   wordpress
-   mysql

Resources involved:

-   Storage volume
-   Secret
-   Service

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Use the prepared storage volume claims. Create two storage volume claims: wordpress-pv-claim and wordpress-mysql-pv-claim which are used in the wordpress yaml file and the wordpress-mysql yaml file respectively, to mount corresponding storage volumes. 
3.  Click **Application** \> **Secret** in the left-side navigation pane, select a cluster and namespace, and click **Create** in the upper-right corner. For the creation process, see [Create a secret](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Config map and Secret management/Create a secret.md#). 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15530676547669_en-US.png)

    Since a user name and password is required to create and access the MySQL database, create a secret to manage the user name and password.

    Before using a secret, create a secret that needs to be encrypted. In this example, the MySQL root password is created as the secret and the secret name is mysql-pass. This secret is used in the WordPress yaml file and wordpress-mysql yaml file.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15530676547693_en-US.png)

4.  Click **Application** \> **Deployment** in the left-side navigation pane, and click **Create by template** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15530676547692_en-US.png)

    Select a cluster and namespace. The yaml file for creating WordPress deployment is as follows:

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: wordpress
      labels:
        app: wordpress
    spec:
      selector:
        matchLabels:
          app: wordpress
          tier: frontend
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: wordpress
            tier: frontend
        spec:
          containers:
          - image: wordpress:4
            name: wordpress
            env:
            - name: WORDPRESS_DB_HOST
              value: wordpress-mysql  #Use the name to point to the mysql to be accessed. The name corresponds to the mysql service name.
            - name: WORDPRESS_DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-pass
                  key: password-wordpress
            ports:
            - containerPort: 80
              name: wordpress
            volumeMounts:
            - name: wordpress-pvc
              mountPath: /var/www/html
          volumes:
          - name: wordpress-pvc
            persistentVolumeClaim:
              claimName: wordpress-pv-claim
    ```

    The yaml file for creating mysql deployment is as follows:

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: wordpress-mysql
      labels:
        app: wordpress
    spec:
      selector:
        matchLabels:
          app: wordpress
          tier: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: wordpress
            tier: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-pass
                  key: password-mysql
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: wordpress-mysql-pvc
              mountPath: /var/lib/mysql
          volumes:
          - name: wordpress-mysql-pvc
            persistentVolumeClaim:
              claimName: wordpress-mysql-pv-claim
    ```

5.  To enable external access for the WordPress, you need to create the access method exposed by the WordPress service. In this example, create the WordPress service of the LoadBalancer type so that Container Service automatically creates Alibaba Cloud Server Load Balancer to provide external access. 

    Create a service named WordPress-mysql for the WordPress mysql so that the WordPress deploymet created on the WordPress mysql can be accessed. As the mysql is called only internally for the WordPress, you do not need to create a LoadBalancer type of service for it.

    For how to create a service, see [Create a service](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a service.md#).

    The yaml file used to create WordPress and mysql service is as follows:

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: wordpress
      labels:
        app: wordpress
    spec:
      ports:
        -port: 80
      selector:
        app: wordpress
        tier: frontend
      type: LoadBalancer
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: wordpress-mysql
      labels:
        app: wordpress
    spec:
      ports:
        - port: 3306
      selector:
        app: wordpress
        tier: mysql
      	  clusterIP: None
    ```

6.  When the deployment is completed, click **Application** \> **Service** in the left-side navigation pane. Locate the WordPress service and view its external endpoint. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15530676547695_en-US.png)

7.  Access the external endpoint of the WordPress service in a browser and you can access the WordPress application through the IP address provided by Server Load Balancer. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15530676557696_en-US.png)


During the configuration of the WordPress application, you can log on to the application by using the password configured in the secret. In addition, the data generated by the container to which the WordPress application belongs is saved in the data storage volume.

