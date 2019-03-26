# 部署有依赖关系的wordpress应用 {#task_dd2_2td_n2b .task}

-   创建一个 Kubernetes 集群。详情参见[快速创建Kubernetes集群](intl.zh-CN/快速入门/基础入门/快速创建Kubernetes集群.md#)。
-   您需要创建存储卷和存储卷声明，关于如何创建存储卷，请参见[使用阿里云云盘](../../../../../intl.zh-CN/用户指南/Kubernetes 集群/存储管理/使用阿里云云盘.md#)、[使用阿里云 NAS](../../../../../intl.zh-CN/用户指南/Kubernetes 集群/存储管理/使用阿里云 NAS.md#)、[使用阿里云 OSS](../../../../../intl.zh-CN/用户指南/Kubernetes 集群/存储管理/使用阿里云 OSS.md#)；关于如何创建存储卷声明，请参见[创建持久化存储卷声明](../../../../../intl.zh-CN/用户指南/Kubernetes 集群/存储声明管理/创建持久化存储卷声明.md#)。在这里我们使用阿里云云盘作为存储卷；在示例中我们选择PV/PVC的方式进行存储卷挂载。在这里创建wordpress-pv-claim和wordpress-mysql-pv-claim两个存储声明，分别会在wordpress和wordpress-mysql的yaml文件中使用这两个声明来挂载相应的存储卷。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15535909157682_zh-CN.png)


本例主要演示如何通过编排模板中的自定义模板创建有依赖关系的应用。

主要组件有：

-   wordpress
-   mysql

涉及到的资源：

-   存储卷管理
-   secret管理
-   服务

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。 
2.  使用准备好的存储卷声明。在这里创建wordpress-pvc和wordpress-mysql-pvc两个存储声明，分别会在wordpress和wordpress-mysql的yaml文件中到这两个声明来挂载相应的存储卷。 
3.  单击左侧导航栏中**应用** \> **保密字典**，选择所需的集群和命名空间，单击右上角**创建**。创建过程请参考[创建密钥](../../../../../intl.zh-CN/用户指南/Kubernetes集群/配置项及密钥管理/创建密钥.md#)。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15535909157669_zh-CN.png)

    由于创建和访问mysql数据库需要用户名密码，所以我们通过创建密钥的方式进行用户名密码的管理。

    在使用secret前，您需要先将需要加密的secret在保密字典里进行创建，在本例中通过将mysql root的密码作为密钥进行创建，创建名称为mysql-pass。该密钥会在后面的wordpress和wordpress-mysql的yaml文件中用到。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15535909157693_zh-CN.png)

4.  单击左侧导航栏中的**应用** \> **部署**，单击右上角**使用模板部署**。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15535909157692_zh-CN.png)

    选择所需的集群和命名空间，创建wordpress deployment的yaml文件如下：

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

    创建mysql deployment的yaml文件如下：

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

5.  为了使wordpress能够被外部访问，我们需要为wordpress创建service对外暴露访问方式，在这里使用LoadBalancer类型进行wordpress service的创建，容器服务会自动创建阿里云负载均衡，为用户提供外部访问。 

    wordpress mysql需要创建名为wordpress-mysql的service，以使在上面创建的wordpress deploymet可以访问到。由于该mysql只为wordpress内部调用，所以不需要为其创建LoadBalancer类型的service。

    创建service的方法请参考[创建服务](../../../../../intl.zh-CN/用户指南/Kubernetes集群/应用管理/创建服务.md#)。

    创建wordpress和mysql service的yaml文件如下：

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

6.  当部署完成后，单击左侧导航栏中的**应用** \> **服务**，找到wordpress服务并查看其外端端点 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15535909157695_zh-CN.png)

7.  在浏览器中访问wordpress服务的外部端点，您就可以通过负载均衡提供的IP地址进行wordpress应用的访问。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16063/15535909157696_zh-CN.png)


在wordpress应用的配置过程中，您可以使用密钥中配置的密码登录应用，此外，wordpress应用所属的容器产生的数据会保存数据卷中。

