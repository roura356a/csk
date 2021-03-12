# Manage applications by using the CLI

This topic describes how to use the command-line interface \(CLI\) to create applications or view containers in which applications run.

The kubectl client is connected to the Container Service for Kubernetes \(ACK\) cluster that you want to manage. For more information, see [Connect to an ACK cluster by using Kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

## Create an application by using the CLI

1.  In the CLI, run the following command to start a container. An NGINX web server is used in this example.

    ```
     kubectl run -it nginx --image=registry.aliyuncs.com/spacexnice/netdia:latest
    ```

2.  Run the following command to create a service entry for this container. The `--type=LoadBalancer` parameter in the command indicates that the system must create a Server Load Balancer \(SLB\) instance for the NGINX container.

    ```
    kubectl expose deployment nginx --port=80 --target-port=80 --type=LoadBalancer
    ```


## View containers by using the CLI

In the CLI, run the `kubectl get pods` command to list all the running containers in the default namespace.

```
NAME                                   READY     STATUS    RESTARTS   AGE
nginx-2721357637-d****                 1/1       Running   1          9h
```

