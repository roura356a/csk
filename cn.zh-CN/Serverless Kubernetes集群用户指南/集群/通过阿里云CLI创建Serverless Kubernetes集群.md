---
keyword: [Serverless Kubernetes集群, 阿里云CLI]
---

# 通过阿里云CLI创建Serverless Kubernetes集群

阿里云CLI是基于阿里云开放API建立的管理工具。您可以通过该工具调用阿里云开放API来管理阿里云产品。

在使用阿里云CLI之前，您需要配置调用阿里云资源所需的凭证信息、地域、语言等。更多信息，请参见[简介]()。

## 安装配置阿里云CLI和kubectl

Cloud Shell默认安装配置了阿里云CLI和账号信息，无需任何额外配置。如果您不使用Cloud Shell，您需要安装和配置如下组件。

-   安装阿里云CLI。
    -   在Linux上安装阿里云CLI，具体操作，请参见[在Linux上安装阿里云CLI]()。
    -   在macOS上安装阿里云CLI。
        -   [在macOS上安装阿里云CLI]()。
        -   您也可以通过包管理器工具安装CLI。请参考[Homebrew](https://brew.sh/)，安装包管理工具后，执行以下命令安装CLI。

            ```
            brew install aliyun-cli
            ```

    -   在Windows上安装阿里云CLI，具体操作，请参见[在Windows上安装阿里云CLI]()。
-   配置阿里云CLI。执行以下命令，创建环境变量，用于存放身份认证信息。

    ```
    aliyun configure
    ```

    预期输出：

    ```
    Configuring profile 'default' in 'AK' authenticate mode...
    Access Key Id []: ************
    Access Key Secret []: ************
    Default Region Id []: cn-beijing
    Default Output Format [json]: json (Only support json)
    Default Language [zh|en] en:
    Saving profile[default] ...Done.
    
    Configure Done!!!
    ..............888888888888888888888 ........=8888888888888888888D=..............
    ...........88888888888888888888888 ..........D8888888888888888888888I...........
    .........,8888888888888ZI: ...........................=Z88D8888888888D..........
    .........+88888888 ..........................................88888888D..........
    .........+88888888 .......Welcome to use Alibaba Cloud.......O8888888D..........
    .........+88888888 ............. ************* ..............O8888888D..........
    .........+88888888 .... Command Line Interface(Reloaded) ....O8888888D..........
    .........+88888888...........................................88888888D..........
    ..........D888888888888DO+. ..........................?ND888888888888D..........
    ...........O8888888888888888888888...........D8888888888888888888888=...........
    ............ .:D8888888888888888888.........78888888888888888888O ..............                    
    ```


安装和设置kubectl客户端，具体操作，请参见[Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

## 创建Serverless Kubernetes集群

1.  创建一个工作目录，并且在工作目录下创建`create.json`文件。

    `create.json`文件请求示例如下：

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

    该示例文件描述了Serverless Kubernetes集群配置信息。更多信息，请参考[创建Serverless Kubernetes集群](/cn.zh-CN/API参考/集群/创建集群/创建Serverless Kubernetes集群.md)。

2.  执行以下命令创建Serverless Kubernetes集群。

    ```
    aliyun cs  POST /clusters --header "Content-Type=application/json" --body "$(cat create.json)"                       
    ```

    预期输出：

    ```
    {
        "cluster_id": "************************",
        "instanceId": "************************",
        "request_id": "**********-****-****-****-************",
        "task_id": "*-************"
    }
    ```

3.  创建成功后，执行以下命令查看集群实例。

    ```
    aliyun cs GET /clusters/<YOUR-CLUSTER-ID>                    
    ```

    预期输出：

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

4.  执行以下命令获取当前的集群配置信息。

    ```
    KUBECONFIG=<YOUR-LOCAL-KUBECONFIG-PATH>
    aliyun cs GET /k8s/$cluster/user_config | jq -r '.config' > $KUBECONFIG
    ```

    预期输出：

    ```
    NAME              STATUS   AGE
    default           Active   7m43s
    kube-node-lease   Active   7m45s
    kube-public       Active   7m45s
    kube-system       Active   7m45s
    ```


## 验证Serverless Kubernetes集群

1.  执行以下命令，部署Nginx应用。

    ```
    kubectl run nginx --image=registry-vpc.cn-shenzhen.aliyuncs.com/acs-sample/nginx:latest
    ```

    预期输出：

    ```
    deployment.apps/nginx created
    ```

2.  执行以下命令，查询Nginx应用状态。

    ```
    kubectl get deploy
    ```

    预期输出：

    ```
     NAME    READY   UP-TO-DATE   AVAILABLE   AGE
     nginx   1/1     1            1           58s
    ```


## 删除资源

-   如果您需要删除Nginx应用，请执行以下命令。

    ```
    kubectl delete deploy nginx
    ```

    预期输出：

    ```
    deployment.extensions "nginx" deleted
    ```

-   您可以执行以下命令删除包含Serverless Kubernetes集群和相关的VPC等资源。

    ```
    aliyun cs DELETE /clusters/************
    ```


[概述](/cn.zh-CN/CLI参考（旧版）/概述.md)

[什么是阿里云CLI？]()

[什么是云命令行？]()

