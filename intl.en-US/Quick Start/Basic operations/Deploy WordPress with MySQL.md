# Deploy WordPress with MySQL

This topic describes how to deploy a WordPress site with a MySQL database on Kubernetes.

-   A cluster of Container Service for Kubernetes \(ACK\) is created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/Quick Start/Basic operations/Create a cluster of ACK Managed Edition.md).
-   Persistent volumes \(PVs\) and persistent volume claims \(PVCs\) are created. For more information about how to create a PV, see [Use Alibaba Cloud disks as volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Use Alibaba Cloud disks as volumes.md), [t18764.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/NAS volumes/Use Alibaba Cloud NAS as volumes.md), and [Use OSS volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/OSS volumes/Use OSS volumes.md). For more information about how to create a PVC, see [Create a Persistent Volume Claim](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Create a Persistent Volume Claim.md). In this topic, an Alibaba Cloud disk is provisioned as a PV and mounted by using a PVC. A PVC named WordPress-pv-claim and a PVC named WordPress-MySQL-pv-claim are created. WordPress-pv-claim is used to mount a PV to the WordPress application. WordPress-MySQL-pv-claim is used to mount a PV to the WordPress-MySQL application.

    ![PVs and PVCs](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3937197951/p7682.png)


This example shows how to use custom orchestration templates to set up a WordPress site with a MySQL database.

The following tools are used:

-   WordPress
-   MySQL

The following Kubernetes resources are used:

-   PV
-   Kubernetes Secrete
-   Kubernetes Service

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Configurations**.

5.  On the **Configurations** page, click the **Secrets** tab. In the upper-right corner of the Secrets tab, click **Create**.

    For more information, see [Create a Secret](/intl.en-US/User Guide for Kubernetes Clusters/Configuration items and key management/Create a Secret.md).

    ![Create a Secret](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3937197951/p7693.png)

    The created PVCs are used when you configure the YAML files of the applications. You can specify WordPress-pvc in the YAML file of the WordPress application to mount the corresponding PV. You can also specify WordPress-MySQL in the YAML file of the WordPress-MySQL application to mount the corresponding PV.

    A Secret is used to manage the usernames and passwords that are required to create and access the MySQL database.

    In this example, a Secret named mysql-pass is created to manage the MySQL root password. The Secret type is set to **Opaque**. You can specify the created Secret in the YAML files of the applications.

6.  In the left-side navigation pane, click **Workload**.

7.  Click the **Deployments** tab. In the upper-right corner of the **Deployments** tab, click **Create from Template**.

    ![Create applications](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3937197951/p7692.png)

    Select the namespace to deploy the application. Use the following YAML template to create a Deployment on which the WordPress application runs.

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
              value: wordpress-mysql  # Specify the MySQL database that is accessed by the WordPress application. You must specify the value as the name of the Service that is created for the WordPress-MySQL application.
            - name: WORDPRESS_DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-pass
                  key: password-mysql
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

    Use the following YAML template to create a Deployment on which the MySQL application runs.

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

8.  Create Services.

    To enable external access to the WordPress application, create a **LoadBalancer** type Service for the WordPress application. ACK will automatically create a Server Load Balancer \(SLB\) instance for the created LoadBalancer type Service. This allows external access to the WordPress application.

    To enable WordPress to access the MySQL database internally, create a ClusterIP type Service for the WordPress-MySQL application. The MySQL database does not require external access. Therefore, you do not need to create a **LoadBalancer** type Service for the WordPress-MySQL application. For more information about how to create a Service, see [Create a service](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Create a service.md).

    Use the following YAML templates to create a Service for the WordPress application and a Service for the WordPress-MySQL application.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: wordpress
      labels:
        app: wordpress
    spec:
      ports:
        - port: 80
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

9.  After the Services are created, go to the details page of the cluster. In the left-side navigation pane, click **Services**.

    On the Services page, you can find the Service that is created for the WordPress application and obtain the external endpoint of the Service.

    ![Services](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3937197951/p7695.png)

10. Enter <external endpoint IP address\>/wp-admin/install.php into the address bar of your browser and press Enter to access WordPress.

    ![Access the external endpoint of the Service.](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3937197951/p7696.png)


When you configure WordPress, you can use the created Secret as the credential for logging on to WordPress. Data generated in the container that runs WordPress is stored in the mounted PV.

