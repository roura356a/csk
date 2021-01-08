---
keyword: [serverless Kubernetes \(ASK\) cluster, Alibaba Cloud CLI]
---

# Create an ASK cluster by using Alibaba Cloud CLI

Alibaba Cloud command-line interface \(CLI\) is a management tool that is developed based on Alibaba Cloud APIs. You can use Alibaba Cloud CLI to call APIs of Alibaba Cloud services.

Before you use Alibaba Cloud CLI, you must configure the settings that are required to call Alibaba Cloud resources. These settings include the credential, region, and language. For more information, see [Overview]().

## Install and set up Alibaba Cloud CLI and kubectl

By default, Cloud Shell is preinstalled with Alibaba Cloud CLI and configured with the account information. You do not need to make any configuration changes. If you do not use Cloud Shell, you must install and set up Alibaba Cloud CLI and kubectl.

-   Install Alibaba Cloud CLI.
    -   Install Alibaba Cloud CLI in Linux. For more information, see [Linux]().
    -   Install Alibaba Cloud CLI in macOS.
        -   [MacOS]().
        -   You can also use a packet manager to install Alibaba Cloud CLI in macOS. For more information, see [Homebrew](https://brew.sh/). After the packet manager is installed, run the following command to install Alibaba Cloud CLI:

            ```
            brew install aliyun-cli
            ```

    -   For more information about how to install Alibaba Cloud CLI in Windows, see [Windows]().
-   Configure Alibaba Cloud CLI. Run the following command to create environment variables that are used to store identity information:

    ```
    aliyun configure
    ```

    Sample output:

    ```
    Configuring profile 'default' in 'AK' authenticate mode...
    Access Key Id []: ************
    Access Key Secret []: ************
    Default Region Id []: cn-beijing
    Default Output Format [json]: json (Only support json)
    Default Language [zh|en] en:
    Saving profile[default] ...Done.
    
    Configure Done!!!
    ..............888888888888888888888 ........ =8888888888888888888D=..............
    ...........88888888888888888888888 ..........D8888888888888888888888I...........
    .........,8888888888888ZI: ........................... =Z88D8888888888D..........
    ......... +88888888 ..........................................88888888D..........
    ......... +88888888 .......Welcome to use Alibaba Cloud.......O8888888D..........
    ......... +88888888 ............. ************* ..............O8888888D..........
    ......... +88888888 .... Command Line Interface(Reloaded) ....O8888888D..........
    ......... +88888888...........................................88888888D..........
    ..........D888888888888DO+. ..........................? ND888888888888D..........
    ...........O8888888888888888888888...........D8888888888888888888888=...........
    ............ .:D8888888888888888888.........78888888888888888888O ..............                    
    ```


Install and set up the kubectl client. For more information, see [Install and set up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## Create an ASK cluster

1.  Create a working directory and a file named `create.json` under the directory.

    The following is an example of the `create.json` file:

    ```
    {
        "cluster_type": "Ask",
        "name": "test-serverless-k8s",
        "region_id": "cn-beijing",
        "zoneid": "cn-beijing-h",
        "nat_gateway": true,
        "private_zone": false,
        "tags": [
            {"key": "env", "value": "test"}
        ]
    }
                            
    ```

    The preceding code block describes the configurations that are required to create a serverless Kubernetes \(ASK\) cluster. For more information, see [Create a serverless cluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a serverless cluster.md).

2.  Run the following command to create an ASK cluster:

    ```
    aliyun cs  POST /clusters --header "Content-Type=application/json" --body "$(cat create.json)"                       
    ```

    ```
    {
        "cluster_id": "************************",
        "instanceId": "************************",
        "request_id": "**********-****-****-****-************",
        "task_id": "*-************"
    }
    ```

3.  After the ASK cluster is created, run the following command to query information about the cluster:

    ```
    aliyun cs GET /clusters/<YOUR-CLUSTER-ID>                    
    ```

    Sample output:

    ```
      {
          "-": "PayByTraffic",
          "cluster_healthy": "",
          "cluster_id": "************************",
          "cluster_spec": "",
          "cluster_type": "Ask",
          "created": "2020-07-23T10:02:18+08:00",
          "current_version": "v1.16.6-aliyun.1",
          "data_disk_category": "cloud",
          "data_disk_size": 0,
          "deletion_protection": false,
          "docker_version": "",
          "enabled_migration": false,
          "external_loadbalancer_id": "lb-*********",
          "gw_bridge": "",
          "init_version": "v1.16.6-aliyun.1",
          "instance_type": "",
          "name": "test-serverless-k8s",
          "need_update_agent": false,
          "network_mode": "vpc",
          "node_status": "",
          "private_zone": false,
          "profile": "ask.v2",
          "region_id": "cn-beijing",
          "resource_group_id": "rg-*********",
          "security_group_id": "sg-*********",
          "size": 0,
          "state": "running",
          "subnet_cidr": "172.16.0.0/16",
          "swarm_mode": false,
          "tags": [
              {
                  "key": "env",
                  "value": "test"
              }
          ],
          "updated": "2020-07-23T10:05:11+08:00",
          "vpc_id": "vpc-*********",
          "vswitch_cidr": "",
          "vswitch_id": "vsw-*********",
          "worker_ram_role_name": ""
      }   
    ```

4.  Run the following command to query the content of the kubeconfig file of the cluster:

    ```
    KUBECONFIG=<YOUR-LOCAL-KUBECONFIG-PATH>
    aliyun cs GET /k8s/$cluster/user_config | jq -r '.config' > $KUBECONFIG
    ```

    Sample output:

    ```
    NAME              STATUS   AGE
    default           Active   7m43s
    kube-node-lease   Active   7m45s
    kube-public       Active   7m45s
    kube-system       Active   7m45s
    ```


## Test the ASK cluster

1.  Run the following command to create an NGINX application:

    ```
    kubectl run nginx --image=registry-vpc.cn-shenzhen.aliyuncs.com/acs-sample/nginx:latest
    ```

    Sample output:

    ```
    deployment.apps/nginx created
    ```

2.  Run the following command to query the state of the NGINX application:

    ```
    kubectl get deploy
    ```

    Sample output:

    ```
     NAME    READY   UP-TO-DATE   AVAILABLE   AGE
     nginx   1/1     1            1           58s
    ```


## Delete and release resources

-   Run the following command to delete the NGINX application:

    ```
    kubectl delete deploy nginx
    ```

    Sample output:

    ```
    deployment.extensions "nginx" deleted
    ```

-   You can also run the following command to delete the ASK cluster and release related resources, such as the virtual private cloud \(VPC\) where the cluster is deployed:

    ```
    aliyun cs DELETE /clusters/************
    ```


[Overview](/intl.en-US/Use Container Service through CLI/Overview.md)

[What is Alibaba Cloud CLI?]()

[What is Cloud Shell?]()

