# Go

本文介绍如何安装Alibaba Cloud ACK SDK for Go和如何快速使用Alibaba Cloud ACK SDK for Go完成常见操作，如创建集群、集群扩容以及添加已有ECS实例至Kubernetes集群。

在安装和使用Alibaba Cloud ACK SDK for Go前，确保您已经完成以下操作：

-   安装Golang的环境。

    阿里云Go SDK支持Go 1.7及以上版本。

-   注册阿里云账号并生成访问密钥（AccessKey）。

    详情参见[创建AccessKey]()。


## 源码地址

请参见以下信息获取源码。

|Go|[alibaba-cloud-sdk-go](https://github.com/aliyun/alibaba-cloud-sdk-go/tree/master)|[README.md](https://github.com/aliyun/alibaba-cloud-sdk-go/blob/master/README.md)|

## 安装Alibaba Cloud ACK SDK for Go

您可以通过以下方式安装阿里云Go SDK：

-   使用Glide（推荐）

    执行以下命令，安装阿里云Go SDK：

    ```
    glide get github.com/aliyun/alibaba-cloud-sdk-go
    ```

-   使用govendor

    执行以下命令，安装阿里云Go SDK：

    ```
    go get -u github.com/aliyun/alibaba-cloud-sdk-go/sdk
    ```


## 示例

-   创建集群

    以下示例指定master节点数为3、worker节点数为3，用于创建一个的新的Kubernetes集群实例。有关更多参数详情，请参见[创建Kubernetes专有版集群](/cn.zh-CN/API参考/集群/创建集群/创建Kubernetes专有版集群.md)。

    ```
    package main
    
    import (
        "fmt"
        "github.com/aliyun/alibaba-cloud-sdk-go/services/cs"
    )
    
    func main() {
        // 创建并初始化client实例。
        // 更多方法请见 https://github.com/aliyun/alibaba-cloud-sdk-go/blob/master/docs/2-Client-CN.md。
        client,err := cs.NewClientWithAccessKey("your_region", "your_access_key_id", "your_access_key_secret")
        if err != nil {
            // Handle exceptions
            fmt.Println("err")
            panic(err)
        }
        // 创建请求。
        request := cs.CreateCreateClusterRequest()
        // 设置创建集群参数。
        // 更多参数细节请见 https://help.aliyun.com/document_detail/87525.html。
        request.FormParams = map[string]string{
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
            "tags":`[]`,
            "addons": `[{"name": "flannel"},{"name": "aliyun-log-controller","config": "{\"sls_project_name\":\"k8s-log-c64f6eab6a1764d3dbee3dc2b9e41****\"}"},{"name": "nginx-ingress-controller", "config": "{\"IngressSlbNetworkType\":\"internet\"}"}]`,
            "node_port_range":"30000-32767",
            "login_password":"test****",
            "cpu_policy":"none",
            "master_count":"3",
            "master_vswitch_ids":`["vsw-2ze48rkq464rsdts****","vsw-2ze48rkq464rsdts1****","vsw-2ze48rkq464rsdts1****"]`,
            "master_instance_types":`["ecs.sn1.medium","ecs.sn1.medium","ecs.sn1.medium"]`,
            "master_system_disk_category":"cloud_efficiency",
            "master_system_disk_size":"40",
            "worker_instance_types":`["ecs.sn2.3xlarge"]`,
            "num_of_nodes":"3",
            "worker_system_disk_category":"cloud_efficiency",
            "worker_system_disk_size":"120",
            "vpcid":"vpc-2zegvl5etah5requ0****",
            "worker_vswitch_ids":`["vsw-2ze48rkq464rsdts****"]`,
            "container_cidr":"172.20.XX.XX/16",
            "service_cidr":"172.21.XX.XX/20",
            "worker_data_disks": `[{"category": "cloud_ssd", "size": 500}]`,
            "master_data_disks": `[{"category": "cloud_ssd", "size": 500}]`,
            "taints":`[{"key": "special", "value": "true", "effect": "NoSchedule"}]`,
        }
    
        // 创建请求。
        response,err := client.CreateCluster(request)
        if err != nil {
            panic(err)
        }
        // 判断是否成功。
        if response.IsSuccess() {
            fmt.Printf("%s | Failed reate cluster, response is %s",response.RequestId, response.GetHttpContentString())
        } else {
            fmt.Printf("%s | Create cluster successfully",response.RequestId)
        }
    }
    ```

-   集群扩容

    以下示例将在集群ID为cf68eabc的集群中扩容一台类型为ecs.c5.xlarge的节点。有关更多参数详情，请参见[扩容集群](/cn.zh-CN/API参考/集群/扩容集群/扩容集群.md)。

    ```
    package main
    
    import (
        "fmt"
        "github.com/aliyun/alibaba-cloud-sdk-go/services/cs"
    )
    
    func main() {
        // 创建并初始化client实例。
        client,err := cs.NewClientWithAccessKey("your_region", "your_access_key_id", "your_access_key_secret")
        if err != nil {
            // Handle exceptions
            fmt.Println("err")
            panic(err)
        }
        // 创建集群扩容请求，并设置参数。
        request := cs.CreateScaleClusterRequest()
        // 更多参数细节请见 https://help.aliyun.com/document_detail/123927.html。
        request.PathParams = map[string]string{
            "ClusterId": "cf68eabc",
        }
        request.FormParams = map[string]string{
            "key_pair":"common", // KeyPair
            "vswitch_ids":`["vsw-uf684tfrpwup8gcsw****"]`,
            "worker_instance_types":`["ecs.c5.xlarge"]`,
            "worker_system_disk_category":"cloud_efficiency",
            "worker_system_disk_size":"120",
            "worker_data_disk":"false",
            "worker_data_disks": `[{"category": "cloud_ssd", "size": 500}]`,
            "count":"1",
        }
    
        // 创建请求。
        response,err := client.ScaleCluster(request)
        if err != nil {
            panic(err)
        }
        // 判断是否成功。
        if response.IsSuccess() {
            fmt.Printf("%s | Failed to scale cluster, response is %s",response.RequestId, response.GetHttpContentString())
        } else {
            fmt.Printf("%s | Successfully scale cluster",response.RequestId)
        }
    }
    ```

-   添加已有ECS实例至Kubernetes集群

    以下示例将实例ID为i-xxxx和i-yyyy的ECS添加到集群cf68eabc中。有关更多参数详情，请参见[添加已有实例到集群](/cn.zh-CN/API参考/节点/添加已有实例到集群.md)。

    ```
    package main
    
    import (
        "fmt"
        "github.com/aliyun/alibaba-cloud-sdk-go/services/cs"
    )
    
    func main() {
        // 创建并初始化client实例。
        client,err := cs.NewClientWithAccessKey("your_region", "your_access_key_id", "your_access_key_secret")
        if err != nil {
            // Handle exceptions
            fmt.Println("err")
            panic(err)
        }
        // 创建请求，并设置参数。
        request := cs.CreateAttachInstancesRequest()
        // 更多参数细节请见 https://help.aliyun.com/document_detail/86807.html。
        request.PathParams = map[string]string{
            "ClusterId": "cf68eabc",
        }
        request.FormParams = map[string]string{
            "password": "Hello1234",
            "instances": `["i-xxxx","i-yyyy"]`,
        }
    
        // 创建请求。
        response,err := client.AttachInstances(request)
        if err != nil {
            panic(err)
        }
        // 判断是否成功。
        if response.IsSuccess() {
            fmt.Printf("Failed to attach intstance to cluster, response is %s", response.GetHttpContentString())
        } else {
            fmt.Printf("Successfully attach intstance to cluster",)
        }
    }
    ```


