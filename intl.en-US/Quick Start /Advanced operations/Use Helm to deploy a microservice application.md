# Use Helm to deploy a microservice application {#concept_xfh_dnr_n2b .concept}

This document describes how to deploy a complex application to Alibaba Cloud Kubernetes Container Service. You can use different methods to deploy a SpringCloud application based on different combinations of infrastructure deployment and application deployment.

## Deployment methods {#section_dgh_2nr_n2b .section}

1.  Deploy infrastructures such as Eureka and ConfigServer together with the application.
2.  Deploy the application after building infrastructures on Container Service

## Sample application PiggyMetrics {#section_egh_2nr_n2b .section}

[PiggyMetrics](https://github.com/sqshq/PiggyMetrics?spm=a2c4e.11153940.blogcont610430.14.40667737Uedu2h) is a SpringCloud application project on GitHub with more than 3400 Stars. The project main body is deployed by using Docker Compose and contains complete source codes and well-built container images. It is a very good SpringCloud containerization example.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15574766287362_en-US.png)

This project contains three business microservices: statistical service, account service, and notification service. Each service corresponds to a separate MongoDB. The microservice architecture diagram\(using the author's original diagram\) is as follows:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15574766287363_en-US.png)

SpringCloud basic components include the registry service \(Eureka service registration\), config service \(configuration management\), gateway \(the API gateway, also the JavaScript Web Interface \), monitor service \(Hystrix Dashboard/Turbine\) and more.

The deployment description file used in this article is on GitHub. If you are interested in the files, see the link: [https://github.com/binblee/PiggyMetrics/tree/master/charts](https://github.com/binblee/PiggyMetrics/tree/master/charts?spm=a2c4e.11153940.blogcont610430.15.40667737Uedu2h).

## Scenario 1 Deploy all services with one-click deployment of helm {#section_hgh_2nr_n2b .section}

PiggyMetrics is deployed to a standalone device in the docker-compose YAML. To deploy PiggyMetrics to the Kubernetes environment, convert the docker-compose YAML to Kubernetes deployment YAML. [Kompose](http://kompose.io/?spm=a2c4e.11153940.blogcont610430.16.40667737Uedu2h) can be used to convert a compose file to the Kubernetes deployment file through one click.

**Note:** The [docker compose](https://github.com/sqshq/PiggyMetrics/blob/master/docker-compose.yml?spm=a2c4e.11153940.blogcont610430.17.40667737Uedu2h&file=docker-compose.yml) template in PiggyMetrics is in version 2.1 that is not supported by kompose. Therefore, change the version of the docker compose file to version 2.

Additionally, remove the syntax that kompose does not support:

```
depends_on:
      config:
        condition: service_healthy  #condition is not supported
```

Add Kubernetes server type annotation：

```
labels: 
      kompose.service.type: loadbalancer
```

For the changed compose file, see [https://github.com/binblee/PiggyMetrics/blob/master/charts/docker-compose.yml](https://github.com/binblee/PiggyMetrics/blob/master/charts/docker-compose.yml?spm=a2c4e.11153940.blogcont610430.18.40667737Uedu2h&file=docker-compose.yml).

Set the environmental variables required for PiggyMetrics deployment before executing kompose.

```
$ export NOTIFICATION_SERVICE_PASSWORD=passw0rd
$ export CONFIG_SERVICE_PASSWORD=passw0rd
$ export STATISTICS_SERVICE_PASSWORD=passw0rd
$ export ACCOUNT_SERVICE_PASSWORD=passw0rd
$ export MONGODB_PASSWORD=passw0rd
$ kompose convert -f docker-compose.yml -o piggymetrics -c
```

The -c option of kompose can generate to the [helm chart](https://docs.helm.sh/chart_template_guide?spm=a2c4e.11153940.blogcont610430.19.40667737Uedu2h) format directory result. Use a [helm](https://helm.sh/?spm=a2c4e.11153940.blogcont610430.20.40667737Uedu2h) command to deploy all services.

```
charts $ helm install -n piggymetrics piggymetrics/
			
```

You can see that the success message is output after deployment.

Try this configuration on your own Minikube or Alibaba Cloud Container Service for Kubernetes: [https://www.aliyun.com/product/kubernetes](https://www.aliyun.com/product/kubernetes?spm=a2c4e.11153940.blogcont610430.21.40667737Uedu2h). After the deployment is completed, go to the service list page, you can see all services, and the access addresses and port numbers exposed by the corresponding LoadBalancer services.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15574766287369_en-US.png)

You can access the PiggyMetrics interface by clicking registry service.

Piggymetrics is a personal financial service that allows you to express beautiful reports after entering your income and expenditure.

Visit the registry service to see all the services registered to the Eureka server.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15574766287371_en-US.png)

Remove PiggyMetrics to prepare for the next experiment:

```
charts $ helm delete --purge piggymetrics
release "piggymetrics" deleted
```

## Scenario 2 Deploy the application to an existing SpringCloud basic component environment {#section_ngh_2nr_n2b .section}

The preceding Scenario 1 shows how to deploy all basic components \(Eureka, Zuul, ConfigServer, and Hystrix Dashboard\) and service applications \(gateway, notification, and statistics\) with one helm chart. In practice, the more common situation is that basic components such as Eureka already exist in the cluster. You only need to deploy, upgrade, and maintain your business applications.

To use the Spring Cloud components in Alibaba Cloud Container Service for Kubernetes, choose **Store** \> **App Catalog**.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15574766287390_en-US.png)

Deploy Eureka services on the App Catalog. Click the **ack-springcloud-eureka** component.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15574766287391_en-US.png)

To view or change the configuration, click **Values**.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15574766287392_en-US.png)

Click **DEPLOY**.

In the left-side navigation pane, choose **Discovery and Load Balancing** \> **Services**. Then, select the target cluster and namespace. You can view that EurekaServer has two examples. The exposed service address is `ack-springcloud-eureka-default-ack-springcloud-eureka-svc`.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15574766287393_en-US.png)

In PiggyMetrics, the EUREKA service that all containers automatically access on startup is called `registry`. In general, the EUREKA service name can be passed as a parameter in the image. In this experiment, do not change any codes or images. Therefore, take another measure, namely, expose Eureka to another service called **registry**.

Use Container Service to deploy the following yaml files.

**Note:** If you change the release name during App Catalog deployment, make the same changes to the followings.

```
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

```
$ kubectl apply -f  registry-svc.yml
```

You can also do this through the console interface:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15574766287394_en-US.png)

In the left-side navigation pane, choose **Discovery and Load Balancing** \> **Services** to verify that the registry is created.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15574766287395_en-US.png)

Copy the helm chart directory of PiggyMetrics to a new directory, piggymetrics-no-eureka. Delete the following two files:

```
templates/registry-deployment.yaml
templates/registry-service.yaml
```

These two files are the yaml files used to deploy Eureka deployment and svc, respectively. As you have used the App Catalog to successfully deploy a new registry service as the basic SpringCloud component, you do not have to repeat the deployment.

Execute the helm command to deploy PiggyMetrics again.

```
$ helm install -n piggymetrics piggymetrics-no-eureka/
```

After all services start, access the registry service and you can see that all PiggyMetrics services are properly registered with EurekaServer.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15574766287397_en-US.png)

The PiggyMetrics application has been deployed to the environment with EurekaServer. Access `GATEWAY` to see the familiar login interface.

