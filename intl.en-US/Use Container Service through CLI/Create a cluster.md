# Create a cluster

You can call this operation to create a cluster of Container Service for Kubernetes \(ACK\) and specify the number of nodes in the ACK cluster.

For more information about the API operation, see [t2021637.md\#]() and [Create a managed cluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a managed cluster.md).

## API request and response formats

Request format

```
aliyun cs  POST /clusters --header "Content-Type=application/json" --body "$(cat create.json)"
```

Request parameters:

-   `--header`: You must set Content-Type to application/json.
-   `--body`: The content that is sent to the server. The content can be saved in a local file and must be in valid JSON format. The `create.json` file contains the following content:

Sample requests

-   **Kubernetes clusters**

```
{
    "cluster_type":"Kubernetes",
    "name":"webService",
    "region_id":"cn-beijing",
    "disable_rollback":true,
    "timeout_mins":60,
    "kubernetes_version":"1.14.8-aliyun.1",
    "snat_entry":true,
    "endpoint_public_access":true,
    "ssh_flags":true,
    "cloud_monitor_flags":true,
    "deletion_protection":false,
    "node_cidr_mask":"26",
    "proxy_mode":"ipvs",
    "tags":[],
    "addons":[{"name":"flannel"},{"name":"arms-prometheus"},{"name":"flexvolume"},{"name":"alicloud-disk-controller"},{"name":"logtail-ds","config":"{"IngressDashboardEnabled":"false"}"},{"name":"ack-node-problem-detector","config":"{"sls_project_name":""}"},{"name":"nginx-ingress-controller","config":"{"IngressSlbNetworkType":"internet"}"}],
    "os_type":"Linux",
    "platform":"CentOS",
    "node_port_range":"30000-32767",
    "key_pair":"sian-sshkey",
    "cpu_policy":"none",
    "master_count":3,
    "master_vswitch_ids":["vsw-2zete8s4qocqg0mf6****","vsw-2zete8s4qocqg0mf6****","vsw-2zete8s4qocqg0mf6****"],
    "master_instance_types":["ecs.n4.large","ecs.n4.large","ecs.n4.large"],
    "master_system_disk_category":"cloud_ssd",
    "master_system_disk_size":120,
    "runtime":{"name":"docker","version":"18.09.2"},
    "worker_instance_types":["ecs.i1.xlarge"],
    "num_of_nodes":1,
    "worker_system_disk_category":"cloud_efficiency",
    "worker_system_disk_size":120,
    "vpcid":"vpc-2zecuu62b9zw7a7q****",
    "worker_vswitch_ids":["vsw-2zete8s4qocqg0mf6****"],
    "container_cidr":"172.20.0.0/16",
    "service_cidr":"172.21.0.0/20"
}
```

    Sample request description

    ```
    If you create a cluster that uses the Terway plug-in, the pod_vswitch_ids parameter is required.
    {
        "cluster_type":"Kubernetes",
        "name":"webService-terway",
        "region_id":"cn-beijing",
        "disable_rollback":true,
        "timeout_mins":60,
        "kubernetes_version":"1.14.8-aliyun.1",
        "snat_entry":true,
        "endpoint_public_access":true,
        "ssh_flags":true,"cloud_monitor_flags":true,
        "deletion_protection":false,
        "proxy_mode":"ipvs",
        "tags":[],
        "addons":[{"name":"terway-eni"},{"name":"flexvolume"},{"name":"alicloud-disk-controller"},{"name":"logtail-ds","config":"{\"IngressDashboardEnabled\":\"false\"}"},{"name":"ack-node-problem-detector","config":"{\"sls_project_name\":\"\"}"},{"name":"nginx-ingress-controller","config":"{\"IngressSlbNetworkType\":\"internet\"}"}],
        "os_type":"Linux",
        "platform":"CentOS",
        "node_port_range":"30000-32767",
        "pod_vswitch_ids":["vsw-2zete8s4qocqg0mf6****"],
        "key_pair":"sian-sshkey",
        "cpu_policy":"none",
        "master_count":3,
        "master_vswitch_ids":["vsw-2zed90q9inwtuyfzd****","vsw-2zed90q9inwtuyfzd****","vsw-2zed90q9inwtuyfzd****"],
        "master_instance_types":["ecs.i1.4xlarge","ecs.i1.4xlarge","ecs.i1.4xlarge"],
        "master_system_disk_category":"cloud_ssd",
        "master_system_disk_size":120,
        "runtime":{"name":"docker","version":"18.09.2"},
        "worker_instance_types":["ecs.i1.4xlarge"],
        "num_of_nodes":1,
        "worker_system_disk_category":"cloud_efficiency",
        "worker_system_disk_size":120,
        "vpcid":"vpc-2zecuu62b9zw7a7qnn2tv",
        "worker_vswitch_ids":["vsw-2zed90q9inwtuyfzd****"],
        "is_enterprise_security_group":true,
        "service_cidr":"172.21.0.0/20"
    }
    ```

-   **Managed Kubernetes clusters**

```
{
    "name":"amk-cluster",
    "cluster_type":"ManagedKubernetes",
    "disable_rollback":true,
    "timeout_mins":60,
    "kubernetes_version":"1.16.9-aliyun.1",
    "region_id":"cn-beijing",
    "snat_entry":true,
    "cloud_monitor_flags":true,
    "endpoint_public_access":true,
    "deletion_protection":true,
    "node_cidr_mask":"26",
    "proxy_mode":"ipvs",
    "tags":[
        {
            "key":"tier",
            "value":"backend"
        }
    ],
    "addons":[{"name":"flannel"},{"name":"csi-plugin"},{"name":"csi-provisioner"},{"name":"logtail-ds","config":"{\"IngressDashboardEnabled\":\"true\"}"},{"name":"ack-node-problem-detector","config":"{\"sls_project_name\":\"\"}"},{"name":"nginx-ingress-controller","config":"{\"IngressSlbNetworkType\":\"internet\"}"},{"name":"arms-prometheus"}],
    "os_type":"Linux",
    "platform":"CentOS",
    "runtime":{
        "name":"docker",
        "version":"19.03.5"
    },
    "worker_instance_types":[
        "ecs.i2.2xlarge"
    ],
    "num_of_nodes":3,
    "worker_system_disk_category":"cloud_efficiency",
    "worker_system_disk_size":120,
    "worker_data_disks":[
        {
            "category":"cloud_efficiency",
            "size":"40",
            "encrypted":"true",
            "auto_snapshot_policy_id":""
        }
    ],
    "worker_instance_charge_type":"PrePaid",
    "worker_period_unit":"Month",
    "worker_period":1,
    "worker_auto_renew":true,
    "worker_auto_renew_period":1,
    "vpcid":"vpc-2zemm8mo5rmdppgqm****",
    "container_cidr":"172.20.0.0/16",
    "service_cidr":"172.21.0.0/20",
    "vswitch_ids":[
        "vsw-2zej67xyhh61oqn7i****"
    ],
    "login_password":"Hello1234",
    "logging_type":"SLS",
    "cpu_policy":"none",
    "taints":[
        {
            "key":"key1",
            "value":"value1",
            "effect":"NoSchedule"
        }
    ],
    "security_group_id":"sg-2zeg3u73kkhtixda****"
}
```

-   **Serverless Kubernetes \(ASK\) clusters**

```
{
    "cluster_type":"Ask",
    "name":"ask-cluster",
    "kubernetes_version":"1.16.9-aliyun.1",
    "region_id":"cn-shenzhen",
    "endpoint_public_access":true,
    "private_zone":true,
    "tags":[
        {
            "key":"tier",
            "value":"frontend"
        }
    ],
    "deletion_protection":true,
    "addons":[
        {
            "name":"logtail-ds"
        }
    ],
    "zone_id":"cn-shenzhen-a",
    "vpc_id":"vpc-wz984yvbd6lck22z3****",
    "vswitch_ids":[
        "vsw-wz9uwxhawmtzg7u9h****"
    ],
    "logging_type":"SLS",
    "security_group_id":"sg-wz9b86l4s7nthi1k****"
}
```


Sample responses

```
{
    "cluster_id": "c61cf530524474386a7ab5a1c192****",
    "request_id": "348D4C9C-9105-4A1B-A86E-B58F0F875575",
    "task_id": "T-5ad724ab94a2b109e8000004"
}
```

