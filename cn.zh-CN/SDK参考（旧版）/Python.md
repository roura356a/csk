# Python

本文介绍如何安装Alibaba Cloud ACK SDK for Python和如何快速使用Alibaba Cloud ACK SDK for Python完成常见操作，如创建集群、集群扩容以及添加已有ECS实例至Kubernetes集群。

在安装和使用Alibaba Cloud ACK SDK for Python前，请确保您已经完成以下操作：

-   安装Python的环境。

    阿里云Python SDK支持Python 2.6.x, 2.7.x和3.x及以上环境。

-   注册阿里云账号并生成访问密钥（AccessKey）。

    详情参见[创建AccessKey]()。


## 源码地址

请参见以下信息获取源码。

|Python|[aliyun-openapi-python-sdk](https://github.com/aliyun/aliyun-openapi-python-sdk)|[README.md](https://github.com/aliyun/aliyun-openapi-python-sdk/blob/master/README.md)|

## 安装Alibaba Cloud ACK SDK for Python

以下示例使用的Python版本为2.6.5+。

1.  安装SDK核心库。

    -   如果您使用Python 2.x，执行以下命令安装阿里云SDK核心库。

        ```
        pip install aliyun-python-sdk-cs
        ```

    -   如果您使用Python 3.x，执行以下命令安装阿里云SDK核心库。

        ```
        pip install aliyun-python-sdk-core-v3
        ```

2.  执行以下命令安装容器服务产品的SDK。

    ```
    pip install aliyun-python-sdk-cs
    ```

    如果提示您没有权限，请切换sudo继续执行以下命令。

    ```
    sudo pip install aliyun-python-sdk-cs
    ```


您也可以在[GitHub](https://github.com/aliyun/aliyun-openapi-python-sdk)下载容器服务的SDK。

## 示例

-   创建集群

    以下示例指定master节点数为3、worker节点数为3，用于创建一个的新的Kubernetes集群实例。有关更多参数详情，请参见[创建Kubernetes专有版集群](/cn.zh-CN/API参考/集群/创建集群/创建Kubernetes专有版集群.md)。

    ```
    #!/usr/bin/python
    # -*- coding: UTF-8 -*-
    
    import json
    from aliyunsdkcore.client import AcsClient
    from aliyunsdkcore.acs_exception.exceptions import ClientException
    from aliyunsdkcore.acs_exception.exceptions import ServerException
    from aliyunsdkcs.request.v20151215 import CreateClusterRequest
    
    # 创建 AcsClient 实例。
    client = AcsClient(
        "<your-access-key-id>",
        "<your-access-key-secret>",
        "<your-region-id>"
    )
    # 创建 request，并设置参数。
    request = CreateClusterRequest.CreateClusterRequest()
    create_body = {
        "cluster_type":"Kubernetes",
        "name":"my-test-Kubernetes-cluster",
        "region_id":"cn-beijing",
        "disable_rollback":"true",
        "timeout_mins":"60",
        "kubernetes_version":"1.12.6-aliyun.1",
        "snat_entry":"true",
        "endpoint_public_access":"false",
        "cloud_monitor_flags":"false",
        "node_cidr_mask":"25",
        "proxy_mode":"iptables",
        "tags":[],
        "addons": [{"name": "flannel"},{"name": "aliyun-log-controller","config": "{\"sls_project_name\":\"k8s-log-c64f6eab6a1764d3dbee3dc2b9e41****\"}"},{"name": "nginx-ingress-controller", "config": "{\"IngressSlbNetworkType\":\"internet\"}"}],
        "node_port_range":"30000-32767",
        "login_password":"test****",
        "cpu_policy":"none",
        "master_count":"3",
        "master_vswitch_ids":["vsw-2ze48rkq464rsdts****","vsw-2ze48rkq464rsdts1****","vsw-2ze48rkq464rsdts1****"],
        "master_instance_types":["ecs.sn1.medium","ecs.sn1.medium","ecs.sn1.medium"],
        "master_system_disk_category":"cloud_efficiency",
        "master_system_disk_size":"40",
        "worker_instance_types":["ecs.sn2.3xlarge"],
        "num_of_nodes":"3",
        "worker_system_disk_category":"cloud_efficiency",
        "worker_system_disk_size":"120",
        "vpcid":"vpc-2zegvl5etah5requ0****",
        "worker_vswitch_ids":["vsw-2ze48rkq464rsdts****"],
        "container_cidr":"172.20.XX.XX/16",
        "service_cidr":"172.21.XX.XX/20",
        "worker_data_disks": [{"category": "cloud_ssd", "size": 500}],
        "master_data_disks": [{"category": "cloud_ssd", "size": 500}],
        "taints":[{"key": "special", "value": "true", "effect": "NoSchedule"}],
    }
    request.set_content(json.dumps(create_body))
    request.set_content_type('application/json')
    # 发起 API 请求并打印返回。
    try:
        response = client.do_action_with_exception(request)
        print(response)
    except ServerException as e:
        print(e)
    except ClientException as e:
        print(e)
    ```

-   集群扩容

    以下示例将在集群<your-cluster-id\>中扩容一台类型为ecs.c5.xlarge的节点。有关更多参数详情，请参见[扩容集群](/cn.zh-CN/API参考/集群/扩容集群/扩容集群.md)。

    ```
    #!/usr/bin/python
    # -*- coding: UTF-8 -*-
    
    import json
    from aliyunsdkcore.client import AcsClient
    from aliyunsdkcore.acs_exception.exceptions import ClientException
    from aliyunsdkcore.acs_exception.exceptions import ServerException
    from aliyunsdkcs.request.v20151215 import ScaleClusterRequest
    
    # 创建 AcsClient 实例。
    client = AcsClient(
        "<your-access-key-id>",
        "<your-access-key-secret>",
        "<your-region-id>"
    )
    clusterId = "<your-cluster-id>"
    # 创建 request，并设置参数。
    request = ScaleClusterRequest.ScaleClusterRequest()
    request.set_ClusterId(clusterId)
    scale_body = {
        "key_pair":"common",
        "vswitch_ids":["vsw-uf684tfrpwup8gcsw****"],
        "worker_instance_types":["ecs.c5.xlarge"],
        "worker_system_disk_category":"cloud_efficiency",
        "worker_system_disk_size":"120",
        "worker_data_disk":"false",
        "worker_data_disks": [{"category": "cloud_ssd", "size": 500}],
        "count":"1",
    }
    request.set_content(json.dumps(scale_body))
    request.set_content_type('application/json')
    # 发起 API 请求并打印返回。
    try:
        response = client.do_action_with_exception(request)
        print(response)
    except ServerException as e:
        print(e)
    except ClientException as e:
        print(e)
    ```

-   添加已有ECS实例至Kubernetes集群

    以下示例将实例ID为i-xxxx和i-yyyy的ECS添加到集群<your-cluster-id\>中。有关更多参数详情，请参见[添加已有实例到集群](/cn.zh-CN/API参考/节点/添加已有实例到集群.md)。

    ```
    #!/usr/bin/python
    # -*- coding: UTF-8 -*-
    
    import json
    from aliyunsdkcore.client import AcsClient
    from aliyunsdkcore.acs_exception.exceptions import ClientException
    from aliyunsdkcore.acs_exception.exceptions import ServerException
    from aliyunsdkcs.request.v20151215 import AttachInstancesRequest
    
    # 创建 AcsClient 实例。
    client = AcsClient(
        "<your-access-key-id>",
        "<your-access-key-secret>",
        "<your-region-id>"
    )
    clusterId = "<your-cluster-id>"
    # 创建 request，并设置参数。
    request = AttachInstancesRequest.AttachInstancesRequest()
    request.set_ClusterId(clusterId)
    attach_body = {
       "password": "Hello1234",
        "instances": ["i-xxxx","i-yyyy"],
    }
    request.set_content(json.dumps(attach_body))
    request.set_content_type('application/json')
    # 发起 API 请求并打印返回。
    try:
        response = client.do_action_with_exception(request)
        print(response)
    except ServerException as e:
        print(e)
    except ClientException as e:
        print(e)
    ```


