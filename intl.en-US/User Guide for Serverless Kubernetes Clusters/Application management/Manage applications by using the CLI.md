# Manage applications by using the CLI

This topic describes how to use the command-line interface \(CLI\) to create applications or view containers in which applications run.

## Prerequisites

The kubectl client is connected to the serverless Kubernetes \(ASK\) cluster that you want to manage. For more information, see [Use kubectl to connect to an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to an ASK cluster.md).

## Create an application by using the CLI

Run the following command to start a container. An NGINX web server is used in this example.

```
kubectl run nginx --image=registry.cn-hangzhou.aliyuncs.com/spacexnice/netdia:latest
```

Run the following command to create a service entry for the container. The `--type=LoadBalancer` setting in the command indicates that a Server Load Balancer \(SLB\) instance is created for the NGINX container.

```
kubectl expose deployment nginx --port=80 --target-port=80 --type=LoadBalancer
```

## View containers by using the CLI

Run the following command to list all running containers in the default namespace:

```
kubectl get pods
```

```
NAME                                   READY     STATUS    RESTARTS   AGE
nginx-2721357637-d****                 1/1       Running   1          9h
```

