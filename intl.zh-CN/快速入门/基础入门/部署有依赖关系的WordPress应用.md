# 部署有依赖关系的WordPress应用

本文主要为您介绍如何部署有依赖关系的WordPress应用。

-   创建一个Kubernetes集群。详情参见[快速创建Kubernetes托管版集群](/intl.zh-CN/快速入门/基础入门/快速创建Kubernetes托管版集群.md)。
-   您需要创建存储卷和存储卷声明。关于如何创建存储卷请参见[云盘存储卷使用说明](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/云盘存储卷/云盘存储卷使用说明.md)、[NAS卷使用说明](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/NAS存储卷/NAS卷使用说明.md)、[OSS卷使用说明](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/OSS存储卷/OSS卷使用说明.md)。如何创建存储卷声明请参见[创建持久化存储卷声明](/intl.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/创建持久化存储卷声明.md)。

    本文示例中使用阿里云云盘作为存储卷，选择PV/PVC的方式进行存储卷挂载。在这里创建wordpress-pv-claim和wordpress-mysql-pv-claim两个存储声明，分别会在WordPress和WordPress-MySQL的YAML文件中使用这两个声明来挂载相应的存储卷。

    ![pvc](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4600338161/p259989.png)


本例主要演示如何通过编排模板中的自定义模板创建有依赖关系的应用。

主要组件有：

-   WordPress
-   MySQL

涉及到的资源：

-   存储卷管理
-   Secret管理
-   服务

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**配置管理** \> **保密字典**。

5.  在**保密字典**页面，单击**创建**。

6.  在**创建**面板中，设置保密字典的相关参数，然后单击**确定**

    使用准备好的存储卷声明。本文创建wordpress-pvc和wordPress-mysql-pvc两个存储声明，分别在WordPress和WordPress-MySQL的YAML文件中，用这两个声明挂载相应的存储卷。由于创建和访问MySQL数据库需要用户名密码，所以需要先通过创建密钥的方式进行用户名密码的管理。

    在使用Secret前，您需要先将需要加密的Secret在保密字典里进行创建，在本例中通过将MySQL root的密码作为密钥进行创建，创建名称为MySQL-pass，类型选择**Opaque**。该密钥会在后面的WordPress和WordPress-MySQL的YAML文件中用到。具体操作，请参见[管理保密字典](/intl.zh-CN/Kubernetes集群用户指南/应用/配置项及保密字典/管理保密字典.md)。

    ![配置密钥页面](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3333659951/p7693.png)

7.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

8.  单击**无状态**页签，然后在**无状态**页面右上角，单击**使用YAML创建资源**。

    创建WordPress deployment的YAML文件如下：

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
              value: wordpress-mysql  #通过名称指向需要访问的mysql，该名称与mysql service的名称相对应。
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

    创建mysql deployment的YAML文件如下：

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

9.  创建服务。

    为了使WordPress能够被外部访问，需要为WordPress创建service对外暴露访问方式，在这里使用**LoadBalancer**类型进行WordPress service的创建，容器服务会自动创建阿里云负载均衡，为用户提供外部访问。

    WordPress MySQL需要创建名为WordPress-MySQL的service，以使在上面创建的WordPress deploymet可以访问到。由于该MySQL只为WordPress内部调用，所以不需要为其创建**LoadBalancer**类型的service。创建service的方法请参见[管理服务](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/管理服务.md)。

    创建WordPress和MySQL service的YAML文件如下：

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

10. 在**服务**页面中，找到WordPress服务并查看其外端端点。

    ![服务列表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4600338161/p7695.png)

11. 在浏览器中输入39.99.XX.XX/wp-admin/install.php，访问WordPress服务的外部端点，您就可以通过负载均衡提供的IP地址来访问WordPress应用。

    此处的39.99.XX.XX为上步骤获取的外部端点的IP地址。

    ![访问外部端点](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3333659951/p7696.png)


在WordPress应用的配置过程中，您可以使用密钥中配置的密码登录应用，此外，WordPress应用所属的容器产生的数据会保存数据卷中。

