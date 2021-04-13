# Query detailed information about a cluster

You can run the arc-cs cluster get <clusterId\> command to query detailed information about a cluster of Container Service for Kubernetes \(ACK\).

For more information about the parameters of the command, see [Query a cluster](/intl.en-US/API Reference/Clusters/Query a cluster.md).

## Command-line interface examples

Sample request:

```
arc-cs cluster get cbfa079eb225c44e7bce86c626f0c****
```

Sample response:

```
{
  "clusterId": "cbfa079eb225c44e7bce86c626f0c****",
  "clusterType": "Kubernetes",
  "created": "2020-11-12T18:51:06+08:00",
  "currentVersion": "1.11.5",
  "deletionProtection": false,
  "dockerVersion": "17.06.2-ce-5",
  "externalLoadbalancerId": "lb-wz9gw2knznrpiv6sj****",
  "instanceType": "ecs.se1ne.xlarge",
  "name": "K8S-f5056244-24d4-11eb-b753-0242ac11****",
  "networkMode": "vpc",
  "regionId": "cn-shenzhen",
  "resourceGroupId": "rg-acfmoiyerhu****",
  "securityGroupId": "sg-wz97gjh27h6jgi41****",
  "size": 5,
  "state": "running",
  "tags": [
    {
      "key": "ack.aliyun.com",
      "value": "cbfa079eb225c44e7bce86c626f0c****"
    }
  ],
  "updated": "2020-11-12T19:04:01+08:00",
  "vpcId": "vpc-wz9rqhtseeylp71x7****",
  "vswitchCidr": "",
  "vswitchId": "vsw-wz999pkyv5ybw4ork****",
  "zoneId": "cn-shenzhen-b"
}
```

