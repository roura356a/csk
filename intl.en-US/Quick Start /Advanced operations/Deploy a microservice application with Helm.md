# Deploy a microservice application with Helm {#concept_1215603 .concept}

This topic describes two methods in which Helm can be used to deploy a microservice application in a Kubernetes cluster created by Alibaba Cloud Container Service for Kubernetes \(ACK\). In this topic, a PiggyMetrics application is deployed as the microservice application.

## Deployment method overview {#section_1bb_w0j_fv3 .section}

The following two methods can be used to deploy a microservice application with Helm:

-   Method 1: Deploy a microservice application and the required basic components in a Kubernetes cluster
-   Method 2: Deploy a microservice application in a Kubernetes cluster that contains the basic Spring Cloud components

## Method 1: Deploy a microservice application and the required basic components in a Kubernetes cluster {#section_xou_47v_9cm .section}

Prerequisites

-   A Kubernetes cluster is created with ACK. For more information, see [Create a Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   The Kubernetes cluster can be accessed by using kubectl. For more information, see [Connect to a Kubernetes cluster by using kubectl](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).
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

        For example, the external port specified in the `auth-mongodb` field must be changed to the following:

        ``` {#codeblock_rrp_g9y_3rr}
        auth-mongodb:
          build: mongodb
          ports:
            - 27017:27017
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

6.  Run the helm delete --purge command to remove the deployed application.

    In this topic, to remove the PiggyMetrics application named `piggy`, run the following command:

    ``` {#codeblock_t97_ztc_9cx}
    helm delete --purge piggy
    ```


## Method 2: Deploy the application in a Kubernetes cluster that contains the basic Spring Cloud components {#section_167_3qa_ttg .section}

The preceding Scenario 1 shows how to deploy all basic components \(Eureka, Zuul, ConfigServer, and Hystrix Dashboard\) and service applications \(gateway, notification, and statistics\) with one helm chart. In practice, the more common situation is that basic components such as Eureka already exist in the cluster. You only need to deploy, upgrade, and maintain your business applications.

To use the Spring Cloud components in Alibaba Cloud Container Service for Kubernetes, choose **Store** \> **App Catalog**.

![App Catalog](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15634440107390_en-US.png)

Deploy Eureka services on the App Catalog. Click the **ack-springcloud-eureka** component.

![Deploy Eureka services](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15634440107391_en-US.png)

To view or change the configuration, click **Values**.

![Deploy Eureka services](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15634440107392_en-US.png)

Click **DEPLOY**.

In the left-side navigation pane, choose **Discovery and Load Balancing** \> **Services**. Then, select the target cluster and namespace. You can view that EurekaServer has two examples. The exposed service address is `ack-springcloud-eureka-default-ack-springcloud-eureka-svc`.

![The IP address of the service](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15634440107393_en-US.png)

In PiggyMetrics, the EUREKA service that all containers automatically access on startup is called `registry`. In general, the EUREKA service name can be passed as a parameter in the image. In this experiment, do not change any codes or images. Therefore, take another measure, namely, expose Eureka to another service called registry.

Use Container Service to deploy the following yaml files.

**Note:** If you change the release name during App Catalog deployment, make the same changes to the followings.

``` {#codeblock_yex_651_o68}
apiVersion: v1
kind: Service
metadata:
  name: registry
spec:
  type: LoadBalancer
  ports:
    - port: 8761
      targetPort: 8761
  selector:
    app: ack-springcloud-eureka-default-ack-springcloud-eureka
    release: ack-springcloud-eureka-default
```

You can use the kubectl command line to create the service:

``` {#codeblock_fdq_1vz_6w9}
$ kubectl apply -f  registry-svc.yml
```

You can also do this through the console interface:

![Create a service by using a template](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15634440107394_en-US.png)

In the left-side navigation pane, choose **Discovery and Load Balancing** \> **Services** to verify that the registry is created.

![Services](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15634440117395_en-US.png)

Copy the helm chart directory of PiggyMetrics to a new directory, piggymetrics-no-eureka. Delete the following two files:

``` {#codeblock_z3s_pz6_vaq}
templates/registry-deployment.yaml
templates/registry-service.yaml
```

These two files are the yaml files used to deploy Eureka deployment and svc, respectively. As you have used the App Catalog to successfully deploy a new registry service as the basic SpringCloud component, you do not have to repeat the deployment.

Execute the helm command to deploy PiggyMetrics again.

``` {#codeblock_nfx_5fu_mod}
$ helm install -n piggymetrics piggymetrics-no-eureka/
```

After all services start, access the registry service and you can see that all PiggyMetrics services are properly registered with EurekaServer.

![PiggyMetrics services are properly registered with EurekaServer](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15634440117397_en-US.png)

The PiggyMetrics application has been deployed to the environment with EurekaServer. Access `GATEWAY` to see the familiar login interface.

