# Cluster management

This topic provides examples on how to perform basic cluster operations, including how to create, scale out, and delete a cluster and how to query the state and nodes of a cluster.

## Install Tea-console

Before you debug the SDK demo, you must install Tea-console:

-   Java SDK

To install Tea-console for Java by using Apache Maven, add the following dependencies to the project files:

```
	<dependency>
  		<groupId>com.aliyun</groupId>
  		<artifactId>tea-console</artifactId>
  		<version>0.0.1</version>
	</dependency>
```

-   Python SDK

To install Tea-console for Python by using Python Package Index \(PyPI\), run the following command:

```
pip install alibabacloud_tea_console
```

-   GO SDK

To install Tea-console for Go, run the following command:

```
go get github.com/alibabacloud-go/tea-console/client:v1.0.0
```

-   TypeScript/Node.js SDK

To install Tea-console for TypeScript in Node.js by using node package manager \(NPM\), run the following command:

```
npm install @alicloud/tea-console@^1.0.0 --save
```

## Demo

```
// This file is auto-generated, don't edit it. Thanks.
package aliyun.com.alibabacloud.CS20151215;

import com.aliyun.tea.*;


public class Client {

    /**
     * Use your AccessKey ID and AcecssKey secret to initialize the client.
     * @param accessKeyId
     * @param accessKeySecret
     * @param regionId
     * @param endpoint
     * @return Client
     * @throws Exception
     */
    public static com.aliyun.cs20151215.Client createClient(String accessKeyId, String accessKeySecret, String regionId) throws Exception {
        com.aliyun.teaopenapi.models.Config config = new com.aliyun.teaopenapi.models.Config();
        // Your AccessKey ID.
        config.accessKeyId = accessKeyId;
        // Your AccessKey secret.
        config.accessKeySecret = accessKeySecret;
        // Your zone ID.
        config.regionId = regionId;
        return new com.aliyun.cs20151215.Client(config);
    }

    /**
     * Create an ACK cluster.
     * @param client
     * @return CreateClusterResponse
     * @throws Exception
     */
    public static com.aliyun.cs20151215.models.CreateClusterResponse createCluster(com.aliyun.cs20151215.Client client) throws Exception {
        com.aliyun.cs20151215.models.CreateClusterRequest createClusterRequest = new com.aliyun.cs20151215.models.CreateClusterRequest()
                .setName("my-test-Kubernetes-cluster")
                .setTimeoutMins(60)
                .setClusterType("ManagedKubernetes")
                .setRegionId("cn-beijing")
                .setVpcid("vpc-2zegvl5etah5requ0****")
                .setContainerCidr("172.20.0.0/16")
                .setServiceCidr("172.21.0.0/20")
                .setLoginPassword("Test_1234")
                .setWorkerInstanceChargeType("PostPaid")
                .setWorkerInstanceTypes(java.util.Arrays.asList(
            "ecs.sn2.3xlarge"
        ))
                .setWorkerVswitchIds(java.util.Arrays.asList(
            "vsw-2ze48rkq464rsdts****"
        ))
                .setWorkerSystemDiskCategory("cloud_efficiency")
                .setWorkerSystemDiskSize(120)
                .setNumOfNodes(3)
                .setOsType("linux");
        return client.createCluster(createClusterRequest);
    }

    /**
     * Query the state of an ACK cluster.
     * @param client
     * @param clusterId
     * @return DescribeClusterDetailResponse
     * @throws Exception
     */
    public static com.aliyun.cs20151215.models.DescribeClusterDetailResponse describeClusterDetail(com.aliyun.cs20151215.Client client, String clusterId) throws Exception {
        return client.describeClusterDetail(clusterId);
    }

    /**
     * Scale out an ACK cluster.
     * @param client
     * @param clusterId
     * @return ScaleOutClusterResponse
     * @throws Exception
     */
    public static com.aliyun.cs20151215.models.ScaleOutClusterResponse scaleOutCluster(com.aliyun.cs20151215.Client client, String clusterId) throws Exception {
        com.aliyun.cs20151215.models.ScaleOutClusterRequest scaleOutClusterRequest = new com.aliyun.cs20151215.models.ScaleOutClusterRequest()
                .setKeyPair("common")
                .setVswitchIds(java.util.Arrays.asList(
            "vsw-uf684tfrpwup8gcsw****"
        ))
                .setWorkerInstanceTypes(java.util.Arrays.asList(
            "ecs.c5.xlarge"
        ))
                .setWorkerSystemDiskCategory("cloud_efficiency")
                .setWorkerSystemDiskSize(120)
                .setWorkerDataDisk(false)
                .setCount(10);
        return client.scaleOutCluster(clusterId, scaleOutClusterRequest);
    }

    /**
     * Query cluster nodes.
     * @param client
     * @param clusterId
     * @return DescribeClusterNodesResponse
     * @throws Exception
     */
    public static com.aliyun.cs20151215.models.DescribeClusterNodesResponse describeClusterNodes(com.aliyun.cs20151215.Client client, String clusterId) throws Exception {
        com.aliyun.cs20151215.models.DescribeClusterNodesRequest describeClusterNodesRequest = new com.aliyun.cs20151215.models.DescribeClusterNodesRequest()
                .setPageSize("18")
                .setPageNumber("1")
                .setNodepoolId("nodepool_id")
                .setState("running");
        return client.describeClusterNodes(clusterId, describeClusterNodesRequest);
    }

    /**
     * Delete an ACK cluster.
     * @param client
     * @param clusterId
     * @return DescribeClusterDetailResponse
     * @throws Exception
     */
    public static com.aliyun.cs20151215.models.DeleteClusterResponse deleteCluster(com.aliyun.cs20151215.Client client, String clusterId) throws Exception {
        com.aliyun.cs20151215.models.DeleteClusterRequest deleteClusterRequest = new com.aliyun.cs20151215.models.DeleteClusterRequest()
                .setRetainResources(java.util.Arrays.asList(
            "sg-2ze6ess9kho6fdn9****"
        ));
        return client.deleteCluster(clusterId, deleteClusterRequest);
    }

    public static void main(String[] args) throws Exception {
        // 1. Initialize the client.
        com.aliyun.cs20151215.Client client = Client.createClient("accessKeyId", "accessKeySecret", "regionId");
        // 2. Create an ACK cluster.
        com.aliyun.cs20151215.models.CreateClusterResponse createClusterRes = Client.createCluster(client);
        String clusterId = createClusterRes.body.clusterId;
        // 3. Query the state of the ACK cluster.
        com.aliyun.cs20151215.models.DescribeClusterDetailResponse describeClusterDetailRes = Client.describeClusterDetail(client, clusterId);
        // 4. Scale out the ACK cluster when it is in the running state.

        while (true) {
            if (com.aliyun.teautil.Common.equalString(describeClusterDetailRes.body.state, "initial")) {
                // If the cluster is in the initial state, it indicates that the cluster has not been created. Set the sleep mode to 3 minutes and try again after the cluster wakes.
                com.aliyun.teautil.Common.sleep(180000);
            } else if (com.aliyun.teautil.Common.equalString(describeClusterDetailRes.body.state, "running")) {
                // Scale out the cluster after it is created.
                com.aliyun.cs20151215.models.ScaleOutClusterResponse scaleClusterRes = Client.scaleOutCluster(client, clusterId);
                com.aliyun.teaconsole.Client.log(scaleClusterRes.body.requestId);
                break;
            } else {
                // Exit if the cluster is in other states.
                return ;
            }

        }
        // 5. Query cluster nodes.
        com.aliyun.cs20151215.models.DescribeClusterNodesResponse describeClusterNodesRes = Client.describeClusterNodes(client, clusterId);
        java.util.Map<String, String> describeClusterNodesResHeaders = describeClusterNodesRes.headers;
        com.aliyun.teaconsole.Client.log(describeClusterNodesResHeaders.get("x-acs-request-id"));
        // 6. Delete the ACK cluster.
        com.aliyun.cs20151215.models.DeleteClusterResponse deleteClusterRes = Client.deleteCluster(client, clusterId);
        java.util.Map<String, String> deleteCLusterResHeaders = deleteClusterRes.headers;
        com.aliyun.teaconsole.Client.log(deleteCLusterResHeaders.get("x-acs-request-id"));
    }
}
```

```
# -*- coding: utf-8 -*-
# This file is auto-generated, don't edit it. Thanks.
import sys

from typing import List

from alibabacloud_cs20151215.client import Client as CSClient
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_cs20151215 import models as cs_models
from alibabacloud_tea_util.client import Client as UtilClient
from alibabacloud_tea_console.client import Client as ConsoleClient


class Client:
    def __init__(self):
        pass

    @staticmethod
    def create_client(
        access_key_id: str,
        access_key_secret: str,
        region_id: str,
    ) -> CSClient:
        """
        Use your AccessKey ID and AcecssKey secret to initialize the client.
        @param access_key_id:
        @param access_key_secret:
        @param region_id:
        @param endpoint:
        @return: Client
        @throws Exception
        """
        config = open_api_models.Config()
        # Your AccessKey ID.
        config.access_key_id = access_key_id
        # Your AccessKey secret.
        config.access_key_secret = access_key_secret
        # Your zone ID.
        config.region_id = region_id
        return CSClient(config)

    @staticmethod
    async def create_client_async(
        access_key_id: str,
        access_key_secret: str,
        region_id: str,
    ) -> CSClient:
        """
        Use your AccessKey ID and AcecssKey secret to initialize the client.
        @param access_key_id:
        @param access_key_secret:
        @param region_id:
        @param endpoint:
        @return: Client
        @throws Exception
        """
        config = open_api_models.Config()
        # Your AccessKey ID.
        config.access_key_id = access_key_id
        # Your AccessKey secret.
        config.access_key_secret = access_key_secret
        # Your zone ID.
        config.region_id = region_id
        return CSClient(config)

    @staticmethod
    def create_cluster(
        client: CSClient,
    ) -> cs_models.CreateClusterResponse:
        """
        Create an ACK cluster.
        @param client:
        @return: CreateClusterResponse
        @throws Exception
        """
        create_cluster_request = cs_models.CreateClusterRequest(
            name='my-test-Kubernetes-cluster',
            timeout_mins=60,
            cluster_type='ManagedKubernetes',
            region_id='cn-beijing',
            vpcid='vpc-2zegvl5etah5requ0****',
            container_cidr='172.20.0.0/16',
            service_cidr='172.21.0.0/20',
            login_password='Test_1234',
            worker_instance_charge_type='PostPaid',
            worker_instance_types=[
                'ecs.sn2.3xlarge'
            ],
            worker_vswitch_ids=[
                'vsw-2ze48rkq464rsdts****'
            ],
            worker_system_disk_category='cloud_efficiency',
            worker_system_disk_size=120,
            num_of_nodes=3,
            os_type='linux'
        )
        return client.create_cluster(create_cluster_request)

    @staticmethod
    async def create_cluster_async(
        client: CSClient,
    ) -> cs_models.CreateClusterResponse:
        """
        Create an ACK cluster.
        @param client:
        @return: CreateClusterResponse
        @throws Exception
        """
        create_cluster_request = cs_models.CreateClusterRequest(
            name='my-test-Kubernetes-cluster',
            timeout_mins=60,
            cluster_type='ManagedKubernetes',
            region_id='cn-beijing',
            vpcid='vpc-2zegvl5etah5requ0****',
            container_cidr='172.20.0.0/16',
            service_cidr='172.21.0.0/20',
            login_password='Test_1234',
            worker_instance_charge_type='PostPaid',
            worker_instance_types=[
                'ecs.sn2.3xlarge'
            ],
            worker_vswitch_ids=[
                'vsw-2ze48rkq464rsdts****'
            ],
            worker_system_disk_category='cloud_efficiency',
            worker_system_disk_size=120,
            num_of_nodes=3,
            os_type='linux'
        )
        return await client.create_cluster_async(create_cluster_request)

    @staticmethod
    def describe_cluster_detail(
        client: CSClient,
        cluster_id: str,
    ) -> cs_models.DescribeClusterDetailResponse:
        """
        Query the state of an ACK cluster.
        @param client:
        @param cluster_id:
        @return: DescribeClusterDetailResponse
        @throws Exception
        """
        return client.describe_cluster_detail(cluster_id)

    @staticmethod
    async def describe_cluster_detail_async(
        client: CSClient,
        cluster_id: str,
    ) -> cs_models.DescribeClusterDetailResponse:
        """
        Query the state of an ACK cluster.
        @param client:
        @param cluster_id:
        @return: DescribeClusterDetailResponse
        @throws Exception
        """
        return await client.describe_cluster_detail_async(cluster_id)

    @staticmethod
    def scale_out_cluster(
        client: CSClient,
        cluster_id: str,
    ) -> cs_models.ScaleOutClusterResponse:
        """
        Scale out an ACK cluster.
        @param client:
        @param cluster_id:
        @return: ScaleOutClusterResponse
        @throws Exception
        """
        scale_out_cluster_request = cs_models.ScaleOutClusterRequest(
            key_pair='common',
            vswitch_ids=[
                'vsw-uf684tfrpwup8gcsw****'
            ],
            worker_instance_types=[
                'ecs.c5.xlarge'
            ],
            worker_system_disk_category='cloud_efficiency',
            worker_system_disk_size=120,
            worker_data_disk=False,
            count=10
        )
        return client.scale_out_cluster(cluster_id, scale_out_cluster_request)

    @staticmethod
    async def scale_out_cluster_async(
        client: CSClient,
        cluster_id: str,
    ) -> cs_models.ScaleOutClusterResponse:
        """
        Scale out an ACK cluster.
        @param client:
        @param cluster_id:
        @return: ScaleOutClusterResponse
        @throws Exception
        """
        scale_out_cluster_request = cs_models.ScaleOutClusterRequest(
            key_pair='common',
            vswitch_ids=[
                'vsw-uf684tfrpwup8gcsw****'
            ],
            worker_instance_types=[
                'ecs.c5.xlarge'
            ],
            worker_system_disk_category='cloud_efficiency',
            worker_system_disk_size=120,
            worker_data_disk=False,
            count=10
        )
        return await client.scale_out_cluster_async(cluster_id, scale_out_cluster_request)

    @staticmethod
    def describe_cluster_nodes(
        client: CSClient,
        cluster_id: str,
    ) -> cs_models.DescribeClusterNodesResponse:
        """
        Query cluster nodes.
        @param client:
        @param cluster_id:
        @return: DescribeClusterNodesResponse
        @throws Exception
        """
        describe_cluster_nodes_request = cs_models.DescribeClusterNodesRequest(
            page_size='18',
            page_number='1',
            nodepool_id='nodepool_id',
            state='running'
        )
        return client.describe_cluster_nodes(cluster_id, describe_cluster_nodes_request)

    @staticmethod
    async def describe_cluster_nodes_async(
        client: CSClient,
        cluster_id: str,
    ) -> cs_models.DescribeClusterNodesResponse:
        """
        Query cluster nodes.
        @param client:
        @param cluster_id:
        @return: DescribeClusterNodesResponse
        @throws Exception
        """
        describe_cluster_nodes_request = cs_models.DescribeClusterNodesRequest(
            page_size='18',
            page_number='1',
            nodepool_id='nodepool_id',
            state='running'
        )
        return await client.describe_cluster_nodes_async(cluster_id, describe_cluster_nodes_request)

    @staticmethod
    def delete_cluster(
        client: CSClient,
        cluster_id: str,
    ) -> cs_models.DeleteClusterResponse:
        """
        Delete an ACK cluster.
        @param client:
        @param cluster_id:
        @return: DescribeClusterDetailResponse
        @throws Exception
        """
        delete_cluster_request = cs_models.DeleteClusterRequest(
            retain_resources=[
                'sg-2ze6ess9kho6fdn9****'
            ]
        )
        return client.delete_cluster(cluster_id, delete_cluster_request)

    @staticmethod
    async def delete_cluster_async(
        client: CSClient,
        cluster_id: str,
    ) -> cs_models.DeleteClusterResponse:
        """
        Delete an ACK cluster.
        @param client:
        @param cluster_id:
        @return: DescribeClusterDetailResponse
        @throws Exception
        """
        delete_cluster_request = cs_models.DeleteClusterRequest(
            retain_resources=[
                'sg-2ze6ess9kho6fdn9****'
            ]
        )
        return await client.delete_cluster_async(cluster_id, delete_cluster_request)

    @staticmethod
    def main(
        args: List[str],
    ) -> None:
        # 1. Initialize the client.
        client = Client.create_client('accessKeyId', 'accessKeySecret', 'regionId')
        # 2. Create an ACK cluster.
        create_cluster_res = Client.create_cluster(client)
        cluster_id = create_cluster_res.body.cluster_id
        # 3. Query the state of the ACK cluster.
        describe_cluster_detail_res = Client.describe_cluster_detail(client, cluster_id)
        # 4. Scale out the ACK cluster when it is in the running state.
        while True:
            if UtilClient.equal_string(describe_cluster_detail_res.body.state, 'initial'):
                # If the cluster is in the initial state, it indicates that the cluster has not been created. Set the sleep mode to 3 minutes and try again after the cluster wakes.
                UtilClient.sleep(180000)
            elif UtilClient.equal_string(describe_cluster_detail_res.body.state, 'running'):
                # Scale out the ACK cluster after it is created.
                scale_cluster_res = Client.scale_out_cluster(client, cluster_id)
                ConsoleClient.log(scale_cluster_res.body.request_id)
                break
            else:
                # Exit if the ACK cluster is in other states.
                return
        # 5. Query cluster nodes.
        describe_cluster_nodes_res = Client.describe_cluster_nodes(client, cluster_id)
        describe_cluster_nodes_res_headers = describe_cluster_nodes_res.headers
        ConsoleClient.log(describe_cluster_nodes_res_headers.get('x-acs-request-id'))
        # 6. Delete the ACK cluster.
        delete_cluster_res = Client.delete_cluster(client, cluster_id)
        delete_cluster_res_headers = delete_cluster_res.headers
        ConsoleClient.log(delete_cluster_res_headers.get('x-acs-request-id'))

    @staticmethod
    async def main_async(
        args: List[str],
    ) -> None:
        # 1. Initialize the client.
        client = await Client.create_client_async('accessKeyId', 'accessKeySecret', 'regionId')
        # 2. Create an ACK cluster.
        create_cluster_res = await Client.create_cluster_async(client)
        cluster_id = create_cluster_res.body.cluster_id
        # 3. Query the cluster state.
        describe_cluster_detail_res = await Client.describe_cluster_detail_async(client, cluster_id)
        # 4. Scale out the ACK cluster when it is in the running state.
        while True:
            if UtilClient.equal_string(describe_cluster_detail_res.body.state, 'initial'):
                # If the cluster is in the initial state, it indicates that the cluster has not been created. Set the sleep mode to 3 minutes and try again after the cluster wakes.
                await UtilClient.sleep_async(180000)
            elif UtilClient.equal_string(describe_cluster_detail_res.body.state, 'running'):
                # Scale out the ACK cluster after it is created.
                scale_cluster_res = await Client.scale_out_cluster_async(client, cluster_id)
                ConsoleClient.log(scale_cluster_res.body.request_id)
                break
            else:
                # Exit if the ACK cluster is in other states.
                return
        # 5. Query cluster nodes.
        describe_cluster_nodes_res = await Client.describe_cluster_nodes_async(client, cluster_id)
        describe_cluster_nodes_res_headers = describe_cluster_nodes_res.headers
        ConsoleClient.log(describe_cluster_nodes_res_headers.get('x-acs-request-id'))
        # 6. Delete the ACK cluster.
        delete_cluster_res = await Client.delete_cluster_async(client, cluster_id)
        delete_cluster_res_headers = delete_cluster_res.headers
        ConsoleClient.log(delete_cluster_res_headers.get('x-acs-request-id'))


if __name__ == '__main__':
    Client.main(sys.argv[1:])
```

```
// This file is auto-generated, don't edit it. Thanks.
package main

import (
  "os"
  cs  "github.com/alibabacloud-go/cs-20151215/client"
  openapi  "github.com/alibabacloud-go/darabonba-openapi/client"
  util  "github.com/alibabacloud-go/tea-utils/service"
  console  "github.com/alibabacloud-go/tea-console/client"
  "github.com/alibabacloud-go/tea/tea"
)


/**
 * Use your AccessKey ID and AcecssKey secret to initialize the client.
 * @param accessKeyId
 * @param accessKeySecret
 * @param regionId
 * @param endpoint
 * @return Client
 * @throws Exception
 */
func CreateClient (accessKeyId *string, accessKeySecret *string, regionId *string) (_result *cs.Client, _err error) {
  config := &openapi.Config{}
  // Your AccessKey ID.
  config.AccessKeyId = accessKeyId
  // Your AccessKey secret.
  config.AccessKeySecret = accessKeySecret
  // Your zone ID.
  config.RegionId = regionId
  _result = &cs.Client{}
  _result, _err = cs.NewClient(config)
  return _result, _err
}

/**
 * Create an ACK cluster.
 * @param client
 * @return CreateClusterResponse
 * @throws Exception
 */
func CreateCluster (client *cs.Client) (_result *cs.CreateClusterResponse, _err error) {
  createClusterRequest := &cs.CreateClusterRequest{
    // The cluster name.
    Name: tea.String("my-test-Kubernetes-cluster"),
    // The timeout period for the system to create the ACK cluster.
    TimeoutMins: tea.Int64(60),
    // The cluster type. In this example, the value is set to ManagedKubernetes.
    ClusterType: tea.String("ManagedKubernetes"),
    // The region where the ACK cluster is deployed.
    RegionId: tea.String("cn-beijing"),
    // VPC ID
    Vpcid: tea.String("vpc-2zegvl5etah5requ0****"),
    // The pod CIDR block.
    ContainerCidr: tea.String("172.20.0.0/16"),
    // The Service CIDR block.
    ServiceCidr: tea.String("172.21.0.0/20"),
    // The SSH password of worker nodes. You can select SSH authentication or key pair authentication.
    LoginPassword: tea.String("Test_1234"),
    // The billing method of worker nodes. Valid values: PrePaid and PostPaid.
    WorkerInstanceChargeType: tea.String("PostPaid"),
    // The instance specifications of worker nodes. You can specify multiple specifications.
    WorkerInstanceTypes: []*string{tea.String("ecs.sn2.3xlarge")},
    // The IDs of one or more vSwitches. You can specify one to five vSwitches.
    WorkerVswitchIds: []*string{tea.String("vsw-2ze48rkq464rsdts****")},
    // The type of the system disk for worker nodes.
    WorkerSystemDiskCategory: tea.String("cloud_efficiency"),
    // The size of the system disk for worker nodes.
    WorkerSystemDiskSize: tea.Int64(120),
    // The number of worker nodes.
    NumOfNodes: tea.Int64(3),
    // The operating system of the nodes that run pods. Example: Linux or Windows.
    OsType: tea.String("linux"),
  }
  _result = &cs.CreateClusterResponse{}
  _body, _err := client.CreateCluster(createClusterRequest)
  if _err ! = nil {
    return _result, _err
  }
  _result = _body
  return _result, _err
}

/**
 * Query the cluster state.
 * @param client
 * @param clusterId
 * @return DescribeClusterDetailResponse
 * @throws Exception
 */
func DescribeClusterDetail (client *cs.Client, clusterId *string) (_result *cs.DescribeClusterDetailResponse, _err error) {
  _result = &cs.DescribeClusterDetailResponse{}
  _body, _err := client.DescribeClusterDetail(clusterId)
  if _err ! = nil {
    return _result, _err
  }
  _result = _body
  return _result, _err
}

/**
 * Scale out an ACK cluster.
 * @param client
 * @param clusterId
 * @return ScaleOutClusterResponse
 * @throws Exception
 */
func ScaleOutCluster (client *cs.Client, clusterId *string) (_result *cs.ScaleOutClusterResponse, _err error) {
  scaleOutClusterRequest := &cs.ScaleOutClusterRequest{
    // The name of the key pair that is used to log on to worker nodes. You can select SSH authentication or key pair authentication.
    KeyPair: tea.String("common"),
    // The IDs of one or more vSwitches. You can specify one to three vSwitches.
    VswitchIds: []*string{tea.String("vsw-uf684tfrpwup8gcsw****")},
    // The instance specifications of worker nodes. You can specify multiple specifications.
    WorkerInstanceTypes: []*string{tea.String("ecs.c5.xlarge")},
    // The type of the system disk for worker nodes.
    WorkerSystemDiskCategory: tea.String("cloud_efficiency"),
    // The size of the system disk for worker nodes.
    WorkerSystemDiskSize: tea.Int32(120),
    // Whether to mount data disks.Valid values: true and false.
    WorkerDataDisk: tea.Bool(false),
    // The number of nodes to be added to the ACK cluster.
    Count: tea.Int32(10),
  }
  _result = &cs.ScaleOutClusterResponse{}
  _body, _err := client.ScaleOutCluster(clusterId, scaleOutClusterRequest)
  if _err ! = nil {
    return _result, _err
  }
  _result = _body
  return _result, _err
}

/**
 * Query cluster nodes.
 * @param client
 * @param clusterId
 * @return DescribeClusterNodesResponse
 * @throws Exception
 */
func DescribeClusterNodes (client *cs.Client, clusterId *string) (_result *cs.DescribeClusterNodesResponse, _err error) {
  describeClusterNodesRequest := &cs.DescribeClusterNodesRequest{
    // The number of entries to return on each page. This parameter is optional.
    PageSize: tea.String("18"),
    // The number of the returned page. This parameter is optional.
    PageNumber: tea.String("1"),
    // The node pool ID. This parameter is optional.
    NodepoolId: tea.String("nodepool_id"),
    // The state of the ACK cluster. 
    State: tea.String("running"),
  }
  _result = &cs.DescribeClusterNodesResponse{}
  _body, _err := client.DescribeClusterNodes(clusterId, describeClusterNodesRequest)
  if _err ! = nil {
    return _result, _err
  }
  _result = _body
  return _result, _err
}

/**
 * Delete an ACK cluster.
 * @param client
 * @param clusterId
 * @return DescribeClusterDetailResponse
 * @throws Exception
 */
func DeleteCluster (client *cs.Client, clusterId *string) (_result *cs.DeleteClusterResponse, _err error) {
  deleteClusterRequest := &cs.DeleteClusterRequest{
    // The resources that are retained after an ACK cluster is deleted. This parameter is optional.
    RetainResources: []*string{tea.String("sg-2ze6ess9kho6fdn9****")},
  }
  _result = &cs.DeleteClusterResponse{}
  _body, _err := client.DeleteCluster(clusterId, deleteClusterRequest)
  if _err ! = nil {
    return _result, _err
  }
  _result = _body
  return _result, _err
}

func _main (args []*string) (_err error) {
  // 1. Initialize the client.
  client, _err := CreateClient(tea.String("accessKeyId"), tea.String("accessKeySecret"), tea.String("regionId"))
  if _err ! = nil {
    return _err
  }

  // 2. Create an ACK cluster.
  createClusterRes, _err := CreateCluster(client)
  if _err ! = nil {
    return _err
  }

  clusterId := createClusterRes.Body.ClusterId
  // 3. Query the state of the ACK cluster.
  describeClusterDetailRes, _err := DescribeClusterDetail(client, clusterId)
  if _err ! = nil {
    return _err
  }

  // 4. Scale out the ACK cluster when it is in the running state.
  for true {
    if tea.BoolValue(util.EqualString(describeClusterDetailRes.Body.State, tea.String("initial"))) {
      // If the cluster is in the initial state, it indicates that the cluster has not been created. Set the sleep mode to 3 minutes and try again after the cluster wakes.
      _err = util.Sleep(tea.Int(180000))
      if _err ! = nil {
        return _err
      }
    } else if tea.BoolValue(util.EqualString(describeClusterDetailRes.Body.State, tea.String("running"))) {
      // Scale out the cluster after it is created.
      scaleClusterRes, _err := ScaleOutCluster(client, clusterId)
      if _err ! = nil {
        return _err
      }

      console.Log(scaleClusterRes.Body.RequestId)
      break
    } else {
      // Exit if the cluster is in other states.
      return _err
    }

  }
  // 5. Query cluster nodes.
  describeClusterNodesRes, _err := DescribeClusterNodes(client, clusterId)
  if _err ! = nil {
    return _err
  }

  describeClusterNodesResHeaders := describeClusterNodesRes.Headers
  console.Log(describeClusterNodesResHeaders["x-acs-request-id"])
  // 6. Delete the ACK cluster.
  deleteClusterRes, _err := DeleteCluster(client, clusterId)
  if _err ! = nil {
    return _err
  }

  deleteCLusterResHeaders := deleteClusterRes.Headers
  console.Log(deleteCLusterResHeaders["x-acs-request-id"])
  return _err
}


func main() {
  err := _main(tea.StringSlice(os.Args))
  if err ! = nil {
    panic(err)
  }
}
```

```
// This file is auto-generated, don't edit it
import CS, * as $CS from '@alicloud/cs20151215';
import OpenApi, * as $OpenApi from '@alicloud/openapi-client';
import Util from '@alicloud/tea-util';
import Console from '@alicloud/tea-console';
import * as $tea from '@alicloud/tea-typescript';


export default class Client {

  /**
   * Use your AccessKey ID and AcecssKey secret to initialize the client.
   * @param accessKeyId
   * @param accessKeySecret
   * @param regionId
   * @param endpoint
   * @return Client
   * @throws Exception
   */
  static async createClient(accessKeyId: string, accessKeySecret: string, regionId: string): Promise<CS> {
    let config = new $OpenApi.Config({ });
    // Your AccessKey ID.
    config.accessKeyId = accessKeyId;
    // Your AccessKey secret.
    config.accessKeySecret = accessKeySecret;
    // Your zone ID.
    config.regionId = regionId;
    return new CS(config);
  }

  /**
   Create an ACK cluster.
   * @param client
   * @return CreateClusterResponse
   * @throws Exception
   */
  static async createCluster(client: CS): Promise<$CS.CreateClusterResponse> {
    let createClusterRequest = new $CS.CreateClusterRequest({
      // The cluster name.
      name: "my-test-Kubernetes-cluster",
      // The timeout period for the system to create the ACK cluster.
      timeoutMins: 60,
      // The cluster type. In this example, the value is set to ManagedKubernetes.
      clusterType: "ManagedKubernetes",
      // The region where the ACK cluster is deployed.
      regionId: "cn-beijing",
      // VPC ID
      vpcid: "vpc-2zegvl5etah5requ0****",
      // The pod CIDR block.
      containerCidr: "172.20.0.0/16",
      // The Service CIDR block.
      serviceCidr: "172.21.0.0/20",
      The SSH password of worker nodes. You can select SSH authentication or key pair authentication.
      loginPassword: "Test_1234",
      // The billing method of worker nodes. Valid values: PrePaid and PostPaid.
      workerInstanceChargeType: "PostPaid",
      // The instance specifications of worker nodes. You can specify multiple specifications.
      workerInstanceTypes: [
        "ecs.sn2.3xlarge"
      ],
      // The IDs of one or more vSwitches. You can specify one to five vSwitches.
      workerVswitchIds: [
        "vsw-2ze48rkq464rsdts****"
      ],
      // The type of the system disk for worker nodes.
      workerSystemDiskCategory: "cloud_efficiency",
      // The size of the system disk for worker nodes.
      workerSystemDiskSize: 120,
      // The number of worker nodes.
      numOfNodes: 3,
      // The operating system of the nodes that run pods. Example: Linux or Windows.
      osType: "linux",
    });
    return await client.createCluster(createClusterRequest);
  }

  /**
   Query the state of an ACK cluster.
   * @param client
   * @param clusterId
   * @return DescribeClusterDetailResponse
   * @throws Exception
   */
  static async describeClusterDetail(client: CS, clusterId: string): Promise<$CS.DescribeClusterDetailResponse> {
    return await client.describeClusterDetail(clusterId);
  }

  /**
   * Scale out an ACK cluster.
   * @param client
   * @param clusterId
   * @return ScaleOutClusterResponse
   * @throws Exception
   */
  static async scaleOutCluster(client: CS, clusterId: string): Promise<$CS.ScaleOutClusterResponse> {
    let scaleOutClusterRequest = new $CS.ScaleOutClusterRequest({
      // The name of the key pair that is used to log on to the worker nodes. You can select SSH authentication or key pair authentication.
      keyPair: "common",
      // The IDs of one or more vSwitches. You can specify one to three vSwitches.
      vswitchIds: [
        "vsw-uf684tfrpwup8gcsw****"
      ],
      // The instance specifications of worker nodes. You can specify multiple specifications.
      workerInstanceTypes: [
        "ecs.c5.xlarge"
      ],
      // The type of the system disk for worker nodes.
      workerSystemDiskCategory: "cloud_efficiency",
      // The size of the system disk for worker nodes.
      workerSystemDiskSize: 120,
      // Whether to mount data disks.Valid values: true and false.
      workerDataDisk: false,
      // The number of nodes to be added to the ACK cluster.
      count: 10,
    });
    return await client.scaleOutCluster(clusterId, scaleOutClusterRequest);
  }

  /**
   * Query cluster nodes.
   * @param client
   * @param clusterId
   * @return DescribeClusterNodesResponse
   * @throws Exception
   */
  static async describeClusterNodes(client: CS, clusterId: string): Promise<$CS.DescribeClusterNodesResponse> {
    let describeClusterNodesRequest = new $CS.DescribeClusterNodesRequest({
      // The number of entries to return on each page. This parameter is optional.
      pageSize: "18",
      // The number of the returned page. This parameter is optional.
      pageNumber: "1",
      // The node pool ID. This parameter is optional.
      nodepoolId: "nodepool_id",
      // The cluster state. 
      state: "running",
    });
    return await client.describeClusterNodes(clusterId, describeClusterNodesRequest);
  }

  /**
   * Delete an ACK cluster.
   * @param client
   * @param clusterId
   * @return DescribeClusterDetailResponse
   * @throws Exception
   */
  static async deleteCluster(client: CS, clusterId: string): Promise<$CS.DeleteClusterResponse> {
    let deleteClusterRequest = new $CS.DeleteClusterRequest({
      // The resources that are retained after a cluster is deleted. This parameter is optional.
      retainResources: [
        "sg-2ze6ess9kho6fdn9****"
      ],
    });
    return await client.deleteCluster(clusterId, deleteClusterRequest);
  }

  static async main(args: string[]): Promise<void> {
    // 1. Initialize the client.
    let client = await Client.createClient("accessKeyId", "accessKeySecret", "regionId");
    // 2. Create an ACK cluster.
    let createClusterRes = await Client.createCluster(client);
    let clusterId = createClusterRes.body.clusterId;
    // 3. Query the state of the cluster.
    let describeClusterDetailRes = await Client.describeClusterDetail(client, clusterId);
    // 4. Scale out the ACK cluster when it is in the running state.

    while (true) {
      if (Util.equalString(describeClusterDetailRes.body.state, "initial")) {
        # If the cluster is in the initial state, it indicates that the cluster has not been created. Set the sleep mode to 3 minutes and try again after the cluster wakes.
        await Util.sleep(180000);
      } else if (Util.equalString(describeClusterDetailRes.body.state, "running")) {
        # Scale out the cluster after it is created.
        let scaleClusterRes = await Client.scaleOutCluster(client, clusterId);
        Console.log(scaleClusterRes.body.requestId);
        break;
      } else {
        # Exit if the cluster is in other states.
        return ;
      }

    }
    // 5. Query cluster nodes.
    let describeClusterNodesRes = await Client.describeClusterNodes(client, clusterId);
    let describeClusterNodesResHeaders = describeClusterNodesRes.headers;
    Console.log(describeClusterNodesResHeaders["x-acs-request-id"]);
    // 6. Delete the ACK cluster.
    let deleteClusterRes = await Client.deleteCluster(client, clusterId);
    let deleteCLusterResHeaders = deleteClusterRes.headers;
    Console.log(deleteCLusterResHeaders["x-acs-request-id"]);
  }

}

Client.main(process.argv.slice(2));
```

