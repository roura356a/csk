# Overview

Service Catalog uses the Open Service Broker API to communicate with service brokers. Service Catalog acts as an intermediary for the Kubernetes API Server to negotiate the initial provisioning and retrieve the credentials necessary for the application to use a managed service.

Applications that run on the cloud platform require access to some basic services, such as databases and application servers. For example, a WordPress application is a web application that requires a database service, such as MariaDB. The traditional way is to configure the MariaDB service on which the application is based in the orchestration template of the WordPress application. This way, you can integrate the MariaDB service with the WordPress application. To develop applications on the cloud in the traditional way, developers must configure and deploy the infrastructure for applications. This increases the cost of application management and migration.

Container Service for Kubernetes \(ACK\) supports and integrates with Service Catalog. Service Catalog aims to connect to and manage the service brokers. Service Catalog also allows you to apply the managed services offered by service brokers to applications that run in Kubernetes clusters. Service Catalog supports a series of infrastructure software. This allows developers to directly use these software and focus on the development of core applications. This way, developers do not need to be concerned about the availability, scalability, and management of the infrastructure software.

For more information about how Service Catalog works, see [Service Catalog](https://kubernetes.io/docs/concepts/service-catalog/).

