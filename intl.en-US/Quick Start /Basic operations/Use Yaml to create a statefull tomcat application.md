# Use Yaml to create a statefull tomcat application {#task_acf_dfp_m2b .task}

-   Create a Kubernetes cluster. For more information, see [Create a Kubernetes cluster quickly](reseller.en-US/Quick Start /Basic operations/Create a Kubernetes cluster quickly.md#).
-   You have created the resource objects involved in this example, such as storage volumes, config maps, secrets, node labels, and other resource objects.

In a Container Service Kubernetes orchestration template, you must define resource objects required for running an application, and combine the resource objects into a complete application by using label selector.

This example shows how to create a tomcat application by customizing a template in an orchestration template. The resource objects involved are as follows:

1.  Storage volumes
2.  Config maps
3.  Secrets
4.  Nodes specified by labels
5.  Health check
6.  Server/Load Balancer

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click **Application** \> **Deployment** in the left-side navigation pane.
3.  On the Deployment page, click **Create by Template** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15934/15573897797225_en-US.png)

4.  Configure the template. **Customize the template** to create a tomcat application. 

    -   **Clusters**: Select a cluster. Resource objects are to be deployed in this cluster.
    -   **Namespace**: Select a namespace to which resource objects belong. The default namespace is default. Except for the underlying computing resources such as nodes and persistent storage volumes, most of the resource objects must act on a namespace.
    -   **Resource Type**: Alibaba Cloud Container Service provides multiple resource types of Kubernetes yaml sample templates, enabling you to quickly deploy resource objects. You can write a template based on the format requirements of Kubernetes yaml orchestration to describe the resource type you want to define.
    -   **Add Deployment**: If you are not familiar with Kubernetes yaml orchestration, click **Add Deployment** to configure through the web interface.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15934/15573897797226_en-US.png)

    1.  First create a basic tomcat template on which this example shows how to configure resource objects in a yaml file. 

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
                image: tomcat # replace it with your exactly &lt;image_name:tags>
                ports:
                - containerPort: 8080
        ```

    2.  Add a storage volume based on the basic template 

        Before adding a data volume, apply for a storage volume and create the storage volume claim. You can apply for a storage volume by using one of the following methods: [Use Alibaba Cloud cloud disks](../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage/Use Alibaba Cloud cloud disks.md#), [Use Alibaba Cloud NAS](../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage/Use Alibaba Cloud NAS.md#), and [Use Alibaba Cloud OSS](../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage/Use Alibaba Cloud OSS.md#).

        Create the storage volume claim after applying for a storage volume. For more information, see [Create a persistent storage volume claim](../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage claim management/Create a persistent storage volume claim.md#). In this example, use Alibaba Cloud cloud disks as the storage volumes and use the cloud disk static storage volumes by using PV/PVC. The PVC name is pvc-yunpan-test.

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
                image: tomcat # replace it with your exactly &lt;image_name:tags>
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

    3.  Add a config map 

        Before using a config map, create a config map. For information about creating and using a config map, see [Use a config map in a pod](../../../../reseller.en-US/User Guide/Kubernetes cluster/Config map/Use a config map in a pod.md#).

        In this example, use the config map name and content in the following sample. The config map name is special-config. The config maps are `SPECIAL_LEVEL:very` and`SPECIAL_TYPE:charm`. Use config maps by means of environment variables.

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
                image: tomcat # replace it with your exactly &lt;image_name:tags>
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

    4.  Add a secret 

        Create a secret first. For more information, see [Create a secret](../../../../reseller.en-US/User Guide/Kubernetes cluster/Secrets/Create a secret.md#).

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
                image: tomcat # replace it with your exactly &lt;image_name:tags>
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

    5.  Add a node 

        When you deploy an application, you can deploy the application on a node with the specific label. For instructions, see [Schedule a pod to a specified node](../../../../reseller.en-US/User Guide/Kubernetes cluster/Application Management/Schedule a pod to a specified node.md#).

        In this example, label a node with group:worker. When the application deployment succeeds, the application is deployed on the labeled node.

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

    6.  Add health check 

        On Container Service platform, you can add health check for the application to check the health status of the application. Use liveness probes and readiness probes to detect the health status of a container in the application.

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
                    - port: 8080
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

    7.  Creates a LoadBalancer type service for the tomcat deployment. 

        To access applications deployed on Container Service from external networks such as the public network, you can expose the application by creating a LoadBalancer type service. A LoadBalancer type service creates Load Balancer on Alibaba Cloud. You can access the application through the Load Balancer IP address.

        For information about creating a service, see [Create a service](../../../../reseller.en-US/User Guide/Kubernetes cluster/Application Management/Create a service.md#).

        In this example, the orchestration template is as follows:

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

5.  After the configuration is completed according to the application requirements, click **Create**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15934/15573897797288_en-US.png)

6.  After the deployment succeeds, choose **Discovery and Load Balancing** \> **Service** in the left navigation pane, and select the tomcat-svc service to view its external endpoint. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15934/15573897797373_en-US.png)

7.  Entering the external endpoint in the browser address bar, you can access the tomcat app welcome page . 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15934/15573897797372_en-US.png)


According to your orchestration template, you can explore features of the tomcat application in storage volumes, secrets, config maps, node scheduling, and health check.

