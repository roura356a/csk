# View all clusters

You can run the arc-cs cluster list command to query all Container Service for Kubernetes \(ACK\) clusters.

## Background information

For more information about this command, see [DescribeClustersV1](/intl.en-US/API Reference/Clusters/DescribeClustersV1.md).

## Examples

Sample request:

```
arc-cs cluster list
```

Sample response:

```
{
    "clusters":[
        {
            "clusterId":"c79583114a89745e3978349e3fda5****",
            "clusterType":"Kubernetes",
            "created":"2020-12-30T19:02:12+08:00",
            "initVersion":"1.16.6-aliyun.1",
            "currentVersion":"1.16.6-aliyun.1",
            "nextVersion":"1.16.9-aliyun.1",
            "deletionProtection":false,
            "dockerVersion":"19.03.5",
            "externalLoadbalancerId":"lb-gw87xdtbuu9c7hw3f****",
            "name":"K8S-738686fa-4a8e-11eb-b743-0242c0a8****",
            "networkMode":"vpc",
            "privateZone":false,
            "profile":"",
            "regionId":"eu-central-1",
            "resourceGroupId":"rg-acfmoiyerhu****",
            "securityGroupId":"sg-gw82uvas7slk54ry****",
            "size":5,
            "state":"delete_failed",
            "subnetCidr":"172.16.0.0/16",
            "tags":[
                {
                    "key":"ack.aliyun.com",
                    "value":"c79583114a89745e3978349e3fda5****"
                }
            ],
            "updated":"2021-01-05T14:25:07+08:00",
            "vpcId":"vpc-gw8er5r9mhz6h6a38****",
            "vswitchId":"vsw-gw8tk6gecif0eu9ky****",
            "workerRamRoleName":"",
            "zoneId":"eu-central-1b",
            "clusterSpec":"",
            "maintenanceWindow":{
                "enable":false,
                "maintenanceTime":"",
                "duration":"",
                "weeklyPeriod":""
            }
        }
    ],
    "pageInfo":{
        "pageNumber":1,
        "pageSize":50,
        "totalCount":1
    }
}
```

