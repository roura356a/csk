# Deploy a microservice application with Helm {#concept_1215603 .concept}

This topic describes two methods in which Helm can be used to deploy a microservice application in a Kubernetes cluster created by Alibaba Cloud Container Service for Kubernetes \(ACK\). In this topic, a PiggyMetrics application is deployed as the microservice application.

## Deployment method overview {#section_1bb_w0j_fv3 .section}

The following two methods can be used to deploy a microservice application with Helm:

-   Method 1: Deploy a microservice application and the required basic components in a Kubernetes cluster
-   Method 2: Deploy a microservice application in a Kubernetes cluster that contains the basic Spring Cloud components

## Sample application PiggyMetrics {#section_q99_r10_ezm .section}

[PiggyMetrics](https://github.com/sqshq/PiggyMetrics?spm=a2c4e.11153940.blogcont610430.14.40667737Uedu2h) is a SpringCloud application project on GitHub with more than 3400 Stars. The project main body is deployed by using Docker Compose and contains complete source codes and well-built container images. It is a very good SpringCloud containerization example.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15646580967362_en-US.png)

This project contains three business microservices: statistical service, account service, and notification service. Each service corresponds to a separate MongoDB. The microservice architecture diagram\(using the author's original diagram\) is as follows:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15646580967363_en-US.png)

SpringCloud basic components include the registry service \(Eureka service registration\), config service \(configuration management\), gateway \(the API gateway, also the JavaScript Web Interface \), monitor service \(Hystrix Dashboard/Turbine\) and more.

## Method 1: Deploy a microservice application and the required basic components in a Kubernetes cluster {#section_xou_47v_9cm .section}

Prerequisites

-   A Kubernetes cluster is created with ACK. For more information, see [Create a Kubernetes cluster](../../../../reseller.en-US//Create a Kubernetes cluster.md#).
-   The Kubernetes cluster can be accessed by using kubectl. For more information, see [Connect to a Kubernetes cluster by using kubectl](../../../../reseller.en-US//Connect to a Kubernetes cluster by using kubectl.md#).
-   Kompose and Helm are installed on your local host. For more information, see [Kompose](http://kompose.io/installation/) and [Helm](https://helm.sh/docs/using_helm/#installing-helm).

Overview

A PiggyMetrics application is defined by two Docker compose files: docker-compose.yml and docker-compose.dev.yml. Therefore, to deploy such an application in a Kubernetes cluster created by ACK, you must use Kompose to convert the two Docker compose files to a Kubernetes configuration file.

You can obtain the Docker compose files of a PiggyMetrics application at [docker-compose](https://github.com/sqshq/piggymetrics/blob/master/docker-compose.yml) and [docker-compose.dev](https://github.com/sqshq/piggymetrics/blob/master/docker-compose.dev.yml).

Procedure

1.  Modify the Docker compose files of the PiggyMetrics application.

    1.  Change the versions of these two Docker compose files from 2.1 to 2.

        **Note:** Kompose cannot convert Docker compose files of V2.1.

    2.  In the file docker-compose.yml, find the following fields that is not supported by Kompose:

        ``` {#codeblock_wjl_waz_2ex}
        depends_on:
          config:
            condition: service_healthy  #condition is not supported
        ```

    3.  In the file docker-compose.yml, replace the fields in the preceding step with the following:

        ``` {#codeblock_99n_ay6_kqy}
        depends_on:
          - config
        labels:
          kompose.service.type: loadbalancer
        ```

    4.  In the file docker-compose.dev.yml, change the external ports of the four MongoDB data bases used by the PiggyMetrics application to 27017.

        **Note:** The target PiggyMetrics application in this topic contains four MongoDB data bases, which are defined by four corresponding fields: `auth-mongodb`, `account-mongodb`, `statistics-mongodb`, and `notification-mongodb`.

    The following is a sample YAML file that is modified:

    ``` {#codeblock_7ee_jdo_9wc}
    version: '2'
    services:
      rabbitmq:
        image: rabbitmq:3-management
        restart: always
        labels:
          kompose.service.type: nodeport
        ports:
          - 5672
          - 15672:15672
        logging:
          options:
            max-size: "10m"
            max-file: "10"
    
      config:
        environment:
          CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
        image: sqshq/piggymetrics-config
        restart: always
        ports:
          - 8888
        logging:
          options:
            max-size: "10m"
            max-file: "10"
    
      registry:
        environment:
          CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
        image: sqshq/piggymetrics-registry
        restart: always
        depends_on:
          - config
        labels:
          kompose.service.type: loadbalancer
        ports:
          - 8761:8761
        logging:
          options:
            max-size: "10m"
            max-file: "10"
    
      gateway:
        environment:
          CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
        image: sqshq/piggymetrics-gateway
        restart: always
        depends_on:
          - config
        labels:
          kompose.service.type: loadbalancer
        ports:
          - 4000:4000
        logging:
          options:
            max-size: "10m"
            max-file: "10"
    
      auth-service:
        environment:
          CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
          NOTIFICATION_SERVICE_PASSWORD: $NOTIFICATION_SERVICE_PASSWORD
          STATISTICS_SERVICE_PASSWORD: $STATISTICS_SERVICE_PASSWORD
          ACCOUNT_SERVICE_PASSWORD: $ACCOUNT_SERVICE_PASSWORD
          MONGODB_PASSWORD: $MONGODB_PASSWORD
        image: sqshq/piggymetrics-auth-service
        restart: always
        ports:
          - 5000
        depends_on:
          - config
        logging:
          options:
            max-size: "10m"
            max-file: "10"
    
      auth-mongodb:
        environment:
          MONGODB_PASSWORD: $MONGODB_PASSWORD
        image: sqshq/piggymetrics-mongodb
        restart: always
        ports:
          - 27017
        logging:
          options:
            max-size: "10m"
            max-file: "10"
    
      account-service:
        environment:
          CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
          ACCOUNT_SERVICE_PASSWORD: $ACCOUNT_SERVICE_PASSWORD
          MONGODB_PASSWORD: $MONGODB_PASSWORD
        image: sqshq/piggymetrics-account-service
        restart: always
        ports:
          - 6000
        depends_on:
          - config
        logging:
          options:
            max-size: "10m"
            max-file: "10"
    
      account-mongodb:
        environment:
          INIT_DUMP: account-service-dump.js
          MONGODB_PASSWORD: $MONGODB_PASSWORD
        image: sqshq/piggymetrics-mongodb
        restart: always
        ports:
          - 27017
        logging:
          options:
            max-size: "10m"
            max-file: "10"
    
      statistics-service:
        environment:
          CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
          MONGODB_PASSWORD: $MONGODB_PASSWORD
          STATISTICS_SERVICE_PASSWORD: $STATISTICS_SERVICE_PASSWORD
        image: sqshq/piggymetrics-statistics-service
        restart: always
        ports:
          - 8888
        depends_on:
          - config
        logging:
          options:
            max-size: "10m"
            max-file: "10"
    
      statistics-mongodb:
        environment:
          MONGODB_PASSWORD: $MONGODB_PASSWORD
        image: sqshq/piggymetrics-mongodb
        restart: always
        ports:
          - 27017
        logging:
          options:
            max-size: "10m"
            max-file: "10"
    
      notification-service:
        environment:
          CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
          MONGODB_PASSWORD: $MONGODB_PASSWORD
          NOTIFICATION_SERVICE_PASSWORD: $NOTIFICATION_SERVICE_PASSWORD
        image: sqshq/piggymetrics-notification-service
        restart: always
        ports:
          - 8000
        depends_on:
          - config
        logging:
          options:
            max-size: "10m"
            max-file: "10"
    
      notification-mongodb:
        image: sqshq/piggymetrics-mongodb
        restart: always
        environment:
          MONGODB_PASSWORD: $MONGODB_PASSWORD
        ports:
          - 27017
        logging:
          options:
            max-size: "10m"
            max-file: "10"
    
      monitoring:
        environment:
          CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
        image: sqshq/piggymetrics-monitoring
        restart: always
        depends_on:
          - config
        labels:
          kompose.service.type: loadbalancer
        ports:
          - 9000:8080
          - 8989:8989
        logging:
          options:
            max-size: "10m"
            max-file: "10"
    ```

2.  Use Kompose to generate the Kubernetes configuration file to deploy the PiggyMetrics application.
    1.  Run the following commands to set the required environments to deploy the application:

        ``` {#codeblock_40w_14a_b3o}
        export NOTIFICATION_SERVICE_PASSWORD=passw0rd
        export CONFIG_SERVICE_PASSWORD=passw0rd
        export STATISTICS_SERVICE_PASSWORD=passw0rd
        export ACCOUNT_SERVICE_PASSWORD=passw0rd
        export MONGODB_PASSWORD=passw0rd
        ```

    2.  Run the following command to convert the Docker compose files to a Kubernetes configuration file:

        ``` {#codeblock_ow7_ka9_kys}
        kompose convert -f docker-compose.yml -f docker-compose.dev.yml -o piggymetrics -c
        								
        ```

3.  Run the helm install command deploy the PiggyMetrics application in the target Kubernetes cluster.

    For example, you can run the following command to deploy an application named `piggy` in the `pm` namespace:

    ``` {#codeblock_vyf_kbb_b24}
    helm install --namespace pm --name piggy piggymetrics/
    ```

4.  Ensure that the version of your local Helm client is the same as the version of the remote Helm server.

    **Note:** If the versions of your local Helm client and the remote Helm server do not match with each, the following error messaged is displayed:

    ``` {#codeblock_wxp_vzg_x65}
    Error: incompatible versions client[v2.14.1] server[v2.11.0]
    ```

    If this error message is not displayed, you can directly ignore this step.

    -   If you use the MAC OS, run the following commands to obtain the version 2.11.0 of the Helm client:

        ``` {#codeblock_xt8_uw1_16h}
        brew unlink kubernetes-helm
        brew install https://raw.githubusercontent.com/Homebrew/homebrew-core/ee94af74778e48ae103a9fb080e26a6a2f62d32c/Formula/kubernetes-helm.rb               
        ```

    -   If you use the Window or Linux OS, install the corresponding version of the Helm client.
5.  Use the IP address of the gateway service to access the deployed application.

    **Note:** The gateway service is one of the services provided by the target application.

    1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
    2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Ingresses and Load Balancing** \> **Service**.
    4.  Select the target cluster and namespace to find the the IP address of the gateway service.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15646580977369_en-US.png)

6.  Run the helm delete --purge command to remove the deployed application.

    In this topic, to remove the PiggyMetrics application named `piggy`, run the following command:

    ``` {#codeblock_t97_ztc_9cx}
    helm delete --purge piggy
    ```


## Method 2: Deploy the application in a Kubernetes cluster that contains the basic Spring Cloud components {#section_167_3qa_ttg .section}

The preceding Scenario 1 shows how to deploy all basic components \(Eureka, Zuul, ConfigServer, and Hystrix Dashboard\) and service applications \(gateway, notification, and statistics\) with one helm chart. In practice, the more common situation is that basic components such as Eureka already exist in the cluster. You only need to deploy, upgrade, and maintain your business applications.

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Marketplace** \> **App Catalog**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15646580977390_en-US.png)

4.  Click **ack-springcloud-eureka**.
5.  In the Deploy area, select the target cluster. Then, click **Create**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15646580977391_en-US.png)

6.  In the left-side navigation pane under Container Service-Kubernetes, choose **Ingresses and Load Balancing** \> **Service**. Then, select the target cluster and namespace.

    **Note:** The service `ack-springcloud-eureka-default-ack-springcloud-eureka-svc` exposes Eureka by using its IP address.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15646580977393_en-US.png)

7.  Access the registry service to verify that all PiggyMetrics services are properly registered with EurekaServer.

    **Note:** 

    -   The PiggyMetrics application is deployed to the EurekaServer environment. Use `GATEWAY` to log on to the application.
    -   You can also deploy a PiggyMetrics application that does not contain Eureka by following the procedures described in [Method 1](#section_xou_47v_9cm).
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15646580977397_en-US.png)


