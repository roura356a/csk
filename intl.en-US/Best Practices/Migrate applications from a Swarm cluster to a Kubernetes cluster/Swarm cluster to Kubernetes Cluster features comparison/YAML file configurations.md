# YAML file configurations

This article describes the comparison of the YAML files that are used to create applications in a Container Service for Swarm cluster and in a Container Service for Kubernetes \(ACK\) cluster.

## Background

The formats of the YAML files that are used to create applications in a Swarm cluster and in an ACK cluster are different.

-   You can use Kompose to convert a YAML file for a Swarm cluster into a YAML file for an ACK cluster. After the YAML file is converted, you must check the content of the file.

    You can obtain Kompose from the [GitHub](https://github.com/AliyunContainerService/kompose) website.

    You can download Kompose at one of the following URLs:

    -   The Kompose download URL for the macOS operating system: [Mac](http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/swarm/kompose-darwin-amd64)
    -   The Kompose download URL for the Linux operating system: [Linux](http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/swarm/kompose-linux-amd64)
    -   The Kompose download URL for the Windows operating system: [Window](http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/swarm/kompose-windows-amd64.exe)
    **Note:** Kompose does not support specific custom labels in ACK. These custom labels are listed in [Table 1](#table_bd5_q3d_bhb). Alibaba Cloud ACK team will continue its development efforts to add support for all custom labels.

    |Label|Related topic|
    |-----|-------------|
    |external|[External ](/intl.en-US/User Guide/Service orchestrations/external.md)|
    |dns\_options|[dns\_options](/intl.en-US/User Guide/Service orchestrations/dns_options.md)|
    |oom\_kill\_disable|[oom\_kill\_disable](/intl.en-US/User Guide/Service orchestrations/oom_kill_disable.md)|
    |affinity:service|[Service deployment constraints \(affinity:service\)](/intl.en-US/User Guide/Service orchestrations/Service deployment constraints (affinity:service).md)|

-   You can also manually modify a YAML file.

This topic describes the comparison of the YAML files that are used in Swarm and in ACK clusters. The sample YAML files that are used in this topic can be modified based on your business requirements.

## YAML file comparison

**Swarm cluster**

The following template is an example of the wordpress-swarm.yaml YAML file that is used in a Swarm cluster. The YAML file is compared with the YAML files that are used in an ACK cluster. You can refer to the numeric marks in the comments for the comparison details.

```
web:        #---1
  image: registry.aliyuncs.com/acs-sample/wordpress:4.5        #---2
  ports:        #---3
    - '80'
  environment:        #---4
    WORDPRESS_AUTH_KEY: changeme            #---5
    WORDPRESS_SECURE_AUTH_KEY: changeme        #---5
    WORDPRESS_LOGGED_IN_KEY: changeme        #---5
    WORDPRESS_NONCE_KEY: changeme            #---5
    WORDPRESS_AUTH_SALT: changeme            #---5
    WORDPRESS_SECURE_AUTH_SALT: changeme        #---5
    WORDPRESS_LOGGED_IN_SALT: changeme        #---5
    WORDPRESS_NONCE_SALT: changeme            #---5
    WORDPRESS_NONCE_AA: changeme            #---5
  restart: always        #---6
  links:            #---7
    - 'db:mysql'
  labels:            #---8
    aliyun.logs: /var/log        #---9
    aliyun.probe.url: http://container/license.txt        #---10
    aliyun.probe.initial_delay_seconds: '10'            #---10
    aliyun.routing.port_80: http://wordpress            #---11
    aliyun.scale: '3'                            #---12
db:             #---1
  image: registry.aliyuncs.com/acs-sample/mysql:5.7        #---2
  environment:        #---4
    MYSQL_ROOT_PASSWORD: password        #---5
  restart: always        #---6
  labels:        #---8
    aliyun.logs: /var/log/mysql        #---9
```

**ACK cluster**

If you deploy the wordpress-swarm.yaml file in a Swarm cluster, a WordPress application is created. In an ACK cluster, a web application and a database application are required to provide the same services as the WordPress application in the Swarm cluster.

To deploy the web and database applications in an ACK cluster, you must create two Deployments and two Services in the ACK cluster. The Services are created to enable access to the Deployments.

You can use the following YAML files to create the Deployment and Service for the web application, which provides the same services as the web service in the Swarm cluster.

**Note:** The following YAML files are only used as examples in comparison with the wordpress-swarm.yaml file in Swarm. You must modify the YAML files based on your business requirements before you deploy them.

-   The following template is an example of the wordpress-kubernetes-web-deployment.yaml file:

    ```
    apiVersion: apps/v1     # The API version.
    kind: Deployment        # The type of the resource that you want to create.
    metadata:
      name: wordpress      #---1 
      labels:            #---8 Labels are only used to identify resources in Kubernetes.
        app: wordpress
    spec:    # The resource details.
      replicas: 2        #---12 The number of replicas.
      selector:  
        matchLabels:
          app: wordpress
          tier: frontend
    strategy:
    type: Recreate
      template:   # The pod details.
        metadata:
          labels:  # The same as the previous labels field.
            app: wordpress
            tier: frontend
        spec:    # The container details.
          containers:    #
          - image: wordpress:4   #---2 The image and its version.
            name: wordpress
            env:    #---4 The environment variable. You can reference ConfigMaps and Secrets through env.
            - name: WORDPRESS_DB_HOST
              value: wordpress-mysql  #---7 The name of the MySQL service that is accessed by the WordPress application.
            - name: WORDPRESS_DB_PASSWORD    #---5 You can use a Secret to store the password.
              valueFrom:
                secretKeyRef:
                  name: mysql-pass
                  key: password-wordpress
            ports:   #---3 The application port.
            - containerPort: 80
              name: wordpress
    livenessProbe:        #add health check    ---10 Enables the health check feature.
              httpGet:
                path: /
                port: 8080
              initialDelaySeconds: 30
              timeoutSeconds: 5
              periodSeconds: 5
            readinessProbe:       #add health check       ---10 Enables the health check feature.
              httpGet:
                path: /
                port: 8080
              initialDelaySeconds: 5
              timeoutSeconds: 1
              periodSeconds: 5
            volumeMounts:  # Mounts the volume to the container.
            - name: wordpress-pvc
              mountPath: /var/www/html
          volumes:   # Creates a persistent volume (PV) and a persistent volume claim (PVC) to use the volume.
          - name: wordpress-pvc
            persistentVolumeClaim:
              claimName: wordpress-pv-claim
    ```

-   The following template is an example of the wordpress-kubernetes-web-service.yaml file:

    ```
    apiVersion: v1   # The API version.
    kind: Service    # The type of the resource that you want to create.
    metadata:
      name: wordpress
      labels:
        app: wordpress
    spec:
      ports:
        - port: 80   # The Service port.
      selector:  # Uses a label to associate the Service with the application.
        app: wordpress
        tier: frontend
      type: LoadBalancer  #---11 The method that is used to access the application. In this example, a LoadBalancer Service is used to automatically create a Server Load Balancer (SLB) instance. 
    ```


You can use the following YAML files to create the Deployment and Service for the database application, which provides the same services as the database service in the Swarm cluster.

**Note:** The following YAML files are only used as examples in comparison with the wordpress-swarm.yaml file in Swarm. You must modify the YAML files before you deploy the YAML files.

-   The following template is an example of the wordpress-kubernetes-db-deployment.yaml file:

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

-   The following template is an example of the wordpress-kubernetes-db-service.yaml file:

    ```
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


