# Deploy dependency-based WordPress applications {#task_dd2_2td_n2b .task}

-   A Kubernetes cluster is created. For more information, see [Create a Kubernetes cluster quickly](reseller.en-US/Quick Start /Basic operations/Create a Kubernetes cluster quickly.md#).
-   A Persistent Volume \(PV\) and Persistent Volume Claim \(PVC\) are created. For more information about how to create a PV, see [Use Alibaba Cloud cloud disk volumes](../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage management/Use Alibaba Cloud cloud disk volumes.md#), [Use NAS file systems of Alibaba Cloud](../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage management/Use NAS file systems of Alibaba Cloud.md#), and [Use Alibaba Cloud OSS volumes](../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage management/Use Alibaba Cloud OSS volumes.md#). For more information about how to create a PVC, see [Create a persistent volume claim](../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage management/Create a Persistent Volume Claim.md#). Use Alibaba Cloud disks as storage volumes. In the example, choose PV/PVC for the storage volume mount. Create two storage volume claims: wordpress-pv-claim and wordpress-mysql-pv-claim which are used in the wordpress yaml file and the wordpress-mysql yaml file respectively, to mount corresponding storage volumes.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15574591687682_en-US.png)


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
3.  In the left-side navigation pane, choose **Configuration** \> **Secrets**, select the target cluster and namespace, and click **Create** in the upper-right corner. For more information, see [Create a secret](../../../../reseller.en-US/User Guide/Kubernetes cluster/Config map and Secret management/Create a secret.md#). 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15574591687669_en-US.png)

    **Note:** A user name and its password is required for creating and accessing a MySQL database. Therefore, you must create a secret to mange the user name and its password for a MySQL database.

    Before using a secret, create a secret that needs to be encrypted. In this example, the MySQL root password is created as the secret, the secret name is set to `mysql-pass`, and the **Opaque**secret type is selected . This secret is used in the WordPress yaml file and wordpress-mysql yaml file.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15574591687693_en-US.png)

4.  In the left-side navigation pane, choose **Applications** \> **Deployments**, and click **Create by Template** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15574591687692_en-US.png)

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

    For more information, see [Create a service](../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a service.md#).

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

6.  When the deployment is completed, choose **Discovery and Load Balancing** \> **Services** in the left-side navigation pane. Locate the WordPress service and view its external endpoint. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15574591687695_en-US.png)

7.  Access the external endpoint of the WordPress service in a browser and you can access the WordPress application through the IP address provided by Server Load Balancer. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15574591687696_en-US.png)


During the configuration of the WordPress application, you can log on to the application by using the password configured in the secret. In addition, the data generated by the container to which the WordPress application belongs is saved in the data storage volume.

