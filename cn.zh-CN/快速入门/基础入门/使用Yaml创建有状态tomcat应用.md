# 使用Yaml创建有状态tomcat应用 {#task_acf_dfp_m2b .task}

-   创建一个 Kubernetes 集群。详情请参见[快速创建Kubernetes集群](cn.zh-CN/快速入门/基础入门/快速创建Kubernetes集群.md#)。
-   您已创建本例中涉及的资源对象，例如存储卷、配置项、密钥、节点标签等。

在容器服务 kubernetes 模板编排中，您需要自己定义一个应用运行所需的资源对象，通过标签选择器等机制，将资源对象组合成一个完整的应用。

本例主要演示如何通过编排模板中的自定义模板进行tomcat应用的创建。主要涉及到的资源对象有：

1.  存储卷
2.  配置项
3.  密钥
4.  指定节点部署
5.  健康检查
6.  服务/负载均衡

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。 
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **部署**，进入部署列表页面。 
3.  单击页面右上角的**使用模板创建**。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15934/15325970217225_zh-CN.png)

4.  对模板进行相关配置，在这里我们选择**自定义模板**进行tomcat应用的创建。 

    -   **集群**：选择目标集群。资源对象将部署在该集群内。
    -   **命名空间**：选择资源对象所属的命名空间，默认是 default。除了节点、持久化存储卷等底层计算资源以外，大多数资源对象需要作用于命名空间。
    -   **示例模板**：阿里云容器服务提供了多种资源类型的 Kubernetes yaml 示例模板，让您快速部署资源对象。您可以根据 Kubernetes Yaml 编排的格式要求自主编写，来描述您想定义的资源类型。
    -   **添加部署**：您如果不熟悉Kubernetes Yaml 的编排，您也可单击**添加部署**，通过web界面配置。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15934/15325970217226_zh-CN.png)

    1.  首先创建一个tomcat基础模板，本例中，将在该编排模板的基础上，展示在yaml文件中配置各个资源对象。 

        ```
        apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
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
                image: tomcat # replace it with your exactly <image_name:tags>
                ports:
                - containerPort: 8080
        ```

    2.  在基础模板的基础上添加存储卷 

        在添加数据卷之前我们需要先进行存储卷的申请和声明；存储卷的申请有三种类型可以选择，分别为：[使用阿里云云盘](../../../../cn.zh-CN/用户指南/Kubernetes 集群/存储管理/使用阿里云云盘.md#)，[使用阿里云 NAS](../../../../cn.zh-CN/用户指南/Kubernetes 集群/存储管理/使用阿里云 NAS.md#)和[使用阿里云 OSS](../../../../cn.zh-CN/用户指南/Kubernetes 集群/存储管理/使用阿里云 OSS.md#)；具体申请方式请参考各自的链接。

        完成存储申请后，您需要存储卷的声明，声明方式参考[创建持久化存储卷声明](../../../../cn.zh-CN/用户指南/Kubernetes 集群/存储声明管理/创建持久化存储卷声明.md#)。在这里我们使用阿里云云盘作为存储卷；在示例中我们选择PV/PVC的方式进行存储卷挂载，PVC名称为pvc-yunpan-test。

        ```
        apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
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
                image: tomcat # replace it with your exactly <image_name:tags>
                ports:
                - containerPort: 8080
                volumeMounts:                 #add volume
                - name: pvc-yunpan-test
                  mountPath: /data
              volumes:                        #add volume
              - name: pvc-yunpan-test
                persistentVolumeClaim:
                  claimName: pvc-yunpan-test
        
        ```

    3.  增加配置项 

        在使用配置项前，我们需要在配置项管理进行配置项的创建，创建过程和使用方法请参考[在 pod 中使用配置项](../../../../cn.zh-CN/用户指南/Kubernetes 集群/配置项/在 pod 中使用配置项.md#)。

        在这本例中，我们使用示例中的配置项名称和内容，配置项名称为special-config，配置项分别为`SPECIAL_LEVEL:very`和`SPECIAL_TYPE:charm`；通过环境变量的方式进行配置项的使用。

        ```
        apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
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
                image: tomcat # replace it with your exactly <image_name:tags>
                ports:
                - containerPort: 8080
                volumeMounts:
                - name: pvc-yunpan-test
                  mountPath: /data
                env:
                - name: SPECIAL_LEVEL_KEY #add configmap
                  valueFrom:
                    configMapKeyRef:
                      name: special-config
                      key: SPECIAL_LEVEL
                - name: SPECIAL_TYPE_KEY #add configmap
                  valueFrom:
                    configMapKeyRef:
                      name: special-config
                      key: SPECIAL_TYPE
              volumes:
              - name: pvc-yunpan-test
                persistentVolumeClaim:
                  claimName: pvc-yunpan-test
        
        ```

    4.  增加密钥（secret）的使用 

        在使用secret前，您需要先将需要加密的secret在保密字典里进行创建，创建过程请参考[创建密钥](../../../../cn.zh-CN/用户指南/Kubernetes 集群/密钥管理/创建密钥.md#)。

        ```
        apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
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
                image: tomcat # replace it with your exactly <image_name:tags>
                ports:
                - containerPort: 8080
                volumeMounts:
                - name: pvc-yunpan-test
                  mountPath: /data
                env:
                - name: SPECIAL_LEVEL_KEY
                  valueFrom:
                    configMapKeyRef:
                      name: special-config
                      key: SPECIAL_LEVEL
                - name: SPECIAL_TYPE_KEY
                  valueFrom:
                    configMapKeyRef:
                      name: special-config
                      key: SPECIAL_TYPE
                - name: SECRET_USERNAME #add secret
                  valueFrom:
                    secretKeyRef:
                      name: account
                      key: username
                - name: SECRET_PASSWORD #add secret
                  valueFrom:
                    secretKeyRef:
                      name: account
                      key: password
              volumes:
              - name: pvc-yunpan-test
                persistentVolumeClaim:
                  claimName: pvc-yunpan-test
        
        ```

    5.  增加节点选择 

        在部署应用的时候，您可以将应用部署在有特定标签的节点上面，具体做法参考[指定节点调度](../../../../cn.zh-CN/用户指南/Kubernetes 集群/应用管理/指定节点调度.md#)；

        在本例中先给一个节点打上group:worker的标签，当部署应用成功后，该应用会部署在有该标签的节点上。

        ```
        apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
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
                image: tomcat
                ports:
                - containerPort: 8080
                volumeMounts:
                - name: pvc-yunpan-test
                  mountPath: /data
                env:
                - name: SPECIAL_LEVEL_KEY
                  valueFrom:
                    configMapKeyRef:
                      name: special-config
                      key: SPECIAL_LEVEL
                - name: SPECIAL_TYPE_KEY
                  valueFrom:
                    configMapKeyRef:
                      name: special-config
                      key: SPECIAL_TYPE
                - name: SECRET_USERNAME
                  valueFrom:
                    secretKeyRef:
                      name: account
                      key: username
                - name: SECRET_PASSWORD
                  valueFrom:
                    secretKeyRef:
                      name: account
                      key: password
              volumes:
              - name: pvc-yunpan-test
                persistentVolumeClaim:
                  claimName: pvc-yunpan-test
              nodeSelector:       #add node selector
                group: worker
        
        ```

    6.  增加健康检查 

        在容器服务平台上，我们可以为应用添加健康检查来检查应用的健康状态。在容器服务平台上我们通过livenessProbe和readinessProbe来检测应用中某个容器的健康状态。

        ```
        apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
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
                image: tomcat
                ports:
                - containerPort: 8080
                livenessProbe:        #add health check
                  httpGet:
                    path: /
                    port: 8080
                  initialDelaySeconds: 30
                  timeoutSeconds: 5
                  periodSeconds: 5
                readinessProbe:       #add health check
                  httpGet:
                    path: /
                    port: 8080
                  initialDelaySeconds: 5
                  timeoutSeconds: 1
                  periodSeconds: 5
                volumeMounts:
                - name: pvc-yunpan-test
                  mountPath: /data
                env:
                - name: SPECIAL_LEVEL_KEY
                  valueFrom:
                    configMapKeyRef:
                      name: special-config
                      key: SPECIAL_LEVEL
                - name: SPECIAL_TYPE_KEY
                  valueFrom:
                    configMapKeyRef:
                      name: special-config
                      key: SPECIAL_TYPE
                - name: SECRET_USERNAME
                  valueFrom:
                    secretKeyRef:
                      name: account
                      key: username
                - name: SECRET_PASSWORD
                  valueFrom:
                    secretKeyRef:
                      name: account
                      key: password
              volumes:
              - name: pvc-yunpan-test
                persistentVolumeClaim:
                  claimName: pvc-yunpan-test
              nodeSelector:
                group: worker
        ```

    7.  为tomcat deployment创建LoadBalancer类型的service。 

        为了可以通过外部如公网访问部署在容器服务上的应用，您可以通过创建LoadBalancer类型的service将该应用暴露出去。LoadBalancer类型的service会创建阿里云上的负载均衡，您可以通过负载均衡的IP地址进行应用的访问。

        创建service的步骤请参考[创建服务](../../../../cn.zh-CN/用户指南/Kubernetes 集群/应用管理/创建服务.md#)。

        本例中，编排模板如下：

        ```
        apiVersion: v1
        kind: Service
        metadata:
          name: tomcat-svc
          labels:
            app: tomcat-svc
        spec:
          selector:
            app: tomcat
          ports:
          - protocol: TCP
            port: 8080
            targetPort: 8080
          type: LoadBalancer
        
        ```

5.  根据应用需求完成配置后，单击**创建**。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15934/15325970217288_zh-CN.png)

6.  部署成功后，单击左侧导航栏中的**服务**，选择需要的tomcat-svc服务，查看外部端点。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15934/15325970217373_zh-CN.png)

7.  您可以在浏览器中访问tomcat应用欢迎页面。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15934/15325970217372_zh-CN.png)


根据您的编排模板情况，您可以探索该tomcat应用在存储卷、密钥、配置项、节点调度和健康检查方面的特性。

