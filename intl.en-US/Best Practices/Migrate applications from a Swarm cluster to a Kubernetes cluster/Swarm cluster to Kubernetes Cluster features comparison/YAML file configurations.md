# YAML file configurations

This topic describes the results when you compare the YAML files. This applies to the YAML files that are used to create applications in a Container Service for Swarm \(ACS\) cluster and the YAML files that are used to create applications in a Container Service for Kubernetes \(ACK\) cluster.

## Background information

The YAML files that are used to create applications in an ACS cluster and in an ACK cluster are provided in different formats.

-   You can use Kompose to convert a YAML file for an ACS cluster into a YAML file for an ACK cluster. After the YAML file is converted, you must check the content of the file.

    You can obtain Kompose from the [GitHub](https://github.com/AliyunContainerService/kompose) official website.

    You can download Kompose at one of the following URLs:

    -   The Kompose download URL for the Mac operating system: [Mac](http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/swarm/kompose-darwin-amd64)
    -   The Kompose download URL for the Linux operating system: [Linux](http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/swarm/kompose-linux-amd64)
    -   The Kompose download URL for the Windows operating system: [Window](http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/swarm/kompose-windows-amd64.exe)
    **Note:** Kompose does not support specific custom labels in ACK. These custom labels are listed in [Table 1](#table_bd5_q3d_bhb). The solutions are being developed by the Alibaba Cloud ACK team to ensure that Kompose supports all custom labels.

    |Label|Reference|
    |-----|---------|
    |external|[External ](/intl.en-US/User Guide/Service orchestrations/external.md)|
    |dns\_options|[dns\_options](/intl.en-US/User Guide/Service orchestrations/dns_options.md)|
    |oom\_kill\_disable|[oom\_kill\_disable](/intl.en-US/User Guide/Service orchestrations/oom_kill_disable.md)|
    |affinity:service|[Service deployment constraints \(affinity:service\)](/intl.en-US/User Guide/Service orchestrations/Service deployment constraints (affinity:service).md)|

-   You can also manually modify a YAML file.

This topic describes the results when you compare the YAML files that are used in an ACS cluster and the YAML files that are used in an ACK cluster. The YAML files in the following example can be modified based on your business requirements.

## Compare YAML files

**ACS cluster**

The following example shows the content of the YAML file wordpress-swarm.yaml that is used in an ACS cluster. The YAML file is compared with the YAML files that are used in an ACK cluster based on the fields that are numbered in the comments.

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

If you deploy the wordpress-swarm.yaml file, a WordPress application is created. This application corresponds to two deployments in an ACK cluster: web and db.

To create this application in an ACK cluster, you must first create two deployments and two services. The services are used to provide access to the deployments.

You can use the following YAML files to create the web deployment and service that correspond to the web service in the ACS cluster.

**Note:** The following YAML files are only used as examples in contrast with the wordpress-swarm.yaml file in the ACS cluster. You must modify the YAML files. Then, you can deploy the YAML files.

-   The following example shows the content of the wordpress-kubernetes-web-deployment.yaml file:

    ```
    apiVersion: apps/v1     # The API version.
    kind: Deployment        # The type of resource that you want to create.
    metadata:
      name: wordpress      #---1 
      labels:            #---8 Labels are only used to identify resources in ACK.
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
          - image: wordpress:4   #---2 The image and related version details.
            name: wordpress
            env:    #---4 The environment variable. You can use ConfigMaps and secrets based on env.
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
          volumes:   # Creates a PV and a PVC to use the volume.
          - name: wordpress-pvc
            persistentVolumeClaim:
              claimName: wordpress-pv-claim
    ```

-   The following example shows the content of the wordpress-kubernetes-web-service.yaml file:

    ```
    apiVersion: v1   # The API version.
    kind: Service    # The type of resource that you want to create.
    metadata:
      name: wordpress
      labels:
        app: wordpress
    spec:
      ports:
        - port: 80   # The service port.
      selector:  # Uses a label to associate the service with the application.
        app: wordpress
        tier: frontend
      type: LoadBalancer  #---11 The method that is used to access the application. In this example, a LoadBalancer service is used to automatically create an SLB instance.
    ```


You can use the following YAML files to create the db deployment and service that correspond to the db service in the ACS cluster.

**Note:** The following YAML files are only used as examples in contrast with the wordpress-swarm.yaml file in ACS. You must modify the YAML files. Then, you can deploy the YAML files.

-   The following example shows the content of the wordpress-kubernetes-db-deployment.yaml file:

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

-   The following example shows the content of the wordpress-kubernetes-db-service.yaml file:

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


