# Manage applications by using commands {#concept_i5q_t5m_vdb .concept}

You can create applications or view containers in applications by using commands.

## Prerequisites {#section_ypb_hvm_vdb .section}

Before using commands to manage applications, [Connect to a Kubernetes cluster by using kubectl](reseller.en-US//Connect to a Kubernetes cluster by using kubectl.md#).

## Create an application by using commands {#section_zpb_hvm_vdb .section}

Run the following statements to run a simple container \(a Nginx Web server in this example\).

``` {#codeblock_28t_rxu_97b}
 root@master # kubectl run -it nginx --image=registry.aliyuncs.com/spacexnice/netdia:latest
```

This command creates a service portal for this container. Specify `--type=LoadBalancer` and an Alibaba Cloud Server Load Balancer route will be created to the Nginx container.

``` {#codeblock_8mt_5r8_3ca}
root@master # kubectl expose deployment nginx --port=80 --target-port=80 --type=LoadBalancer
```

## View containers by using commands {#section_cqb_hvm_vdb .section}

Run the following command to list all the running containers in the default namespaces.

``` {#codeblock_csu_v6m_yqn}
root@master # kubectl get pods
NAME READY STATUS RESTARTS AGE
nginx-2721357637-dvwq3 1/1 Running 1 9h
```

