# Query nodes in a cluster

You can run the following command to query nodes in a cluster: `arc-cs node list <cluster_id> --instance-ids '["<instance_id1>","<instance_id2>"]'`.

For more information about this command, see [Query resources in a cluster](/intl.en-US/API Reference/Clusters/Query resources in a cluster.md).

## Example by using Alibaba Cloud CLI

Sample request:

```
arc-cs node list c2cb0e7f03bc443c7b6ea164b782d**** \
--instance-ids '["i-j6c6y9bro4zzszdg****","i-j6c6y9bro4zzszdg****"]'
```

Sample response:

```
{
  "nodes": [
    {
      "creationTime": "2021-03-08T15:05:16+08:00",
      "errorMessage": "",
      "expiredTime": "2099-12-31T23:59:00+08:00",
      "hostName": "iZj6c6y9bro4zzszdg5****",
      "imageId": "aliyun_2_1903_x64_20G_alibase_20210120.vhd",
      "instanceChargeType": "PostPaid",
      "instanceId": "i-j6c6y9bro4zzszdg****",
      "instanceName": "worker-k8s-for-cs-c2cb0e7f03bc443c7b6ea164b782d****",
      "instanceRole": "Worker",
      "instanceStatus": "Running",
      "instanceType": "ecs.mn4.large",
      "instanceTypeFamily": "ecs.mn4",
      "ipAddress": [
        "192.168.0.5"
      ],
      "isAliyunNode": true,
      "nodeName": "cn-hongkong.192.168.0.5",
      "nodeStatus": "Ready",
      "nodepoolId": "npcff628b7643e412a8854ec298405****",
      "source": "ess",
      "state": "running"
    },
    {
      "creationTime": "2021-03-08T15:05:16+08:00",
      "errorMessage": "",
      "expiredTime": "2099-12-31T23:59:00+08:00",
      "hostName": "iZj6c6y9bro4zzszdg****",
      "imageId": "aliyun_2_1903_x64_20G_alibase_20210120.vhd",
      "instanceChargeType": "PostPaid",
      "instanceId": "i-j6c6y9bro4zzszdg5srd",
      "instanceName": "worker-k8s-for-cs-c2cb0e7f03bc443c7b6ea164b782****",
      "instanceRole": "Worker",
      "instanceStatus": "Running",
      "instanceType": "ecs.mn4.large",
      "instanceTypeFamily": "ecs.mn4",
      "ipAddress": [
        "192.168.0.6"
      ],
      "isAliyunNode": true,
      "nodeName": "cn-hongkong.192.168.0.6",
      "nodeStatus": "Ready",
      "nodepoolId": "npcff628b7643e412a8854ec29841****",
      "source": "ess",
      "state": "running"
    }
  ],
  "page": {
    "pageNumber": 1,
    "pageSize": 10,
    "totalCount": 2
  }
}
```

