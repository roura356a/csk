# Manage applications by using commands {#concept_dsw_jd1_ydb .concept}

You can create applications or view containers in applications by using commands.

## Prerequisites {#section_ykk_rd1_ydb .section}

Before using commands, configure [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Serverless Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#) first.

## Create an application by using commands {#section_zkk_rd1_ydb .section}

Execute the following statements to run a simple container \(a Nginx Web server in this example\).

```
root@master # kubectl run nginx --image=registry.cn-hangzhou.aliyuncs.com/spacexnice/netdia:latest
```

This command creates a service portal for this container. Specify`--type=LoadBalancer` and Alibaba Cloud Server Load Balancer route will be created to the Nginx container.

```
root@master # kubectl expose deployment nginx --port=80 --target-port=80 --type=LoadBalancer
```

## View containers by using commands {#section_clk_rd1_ydb .section}

Run the following command to list all the running containers in the default namespaces.

```
root@master # kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
nginx-2721357637-dvwq3                 1/1       Running   1          9h
```

