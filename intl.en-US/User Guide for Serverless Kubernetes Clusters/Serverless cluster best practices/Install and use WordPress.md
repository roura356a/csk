# Install and use WordPress

[Cloud Shell](https://shell.aliyun.com/?action=git_open&git_repo=https://code.aliyun.com/qinglin.dql/eci-wordpress.git&tutorial=tutorial-zh.md)

**Note:** You can clone the sample code for this topic to Cloud Shell and learn how to manage an ECI in Cloud Shell. Click the preceding link to open Cloud Shell.

## Create a serverless Kubernetes cluster

For more information, see [Create a serverless Kubernetes cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

## Install WordPress

**Note:** Make sure that the serverless Kubernetes cluster created in the preceding step is initialized before you install WordPress. Typically, the initialization takes about 3 to 5 minutes.

Access the serverless Kubernetes cluster in Cloud Shell.

```
source use-k8s-cluster ${Cluster ID}
```

Install WordPress by using a YAML file.

```
kubectl apply -f wordpress-all-in-one-pod.yaml
```

Check the installation progress. Wait until the status of the pod where WordPress is deployed turns to Running.

```
kubectl get pods
```

Query the Elastic IP Address \(EIP\) of the pod.

```
kubectl get -o json pod wordpress |grep "k8s.aliyun.com/allocated-eipAddress"
```

By default, a security group does not allow access through port 80. You must enable access through port 80 for the security group automatically created for the serverless Kubernetes cluster.

Find the ID of the recently created security group whose name is prefixed with `alicloud-cs-auto-created`.

```
aliyun ecs DescribeSecurityGroups|grep -B 1 'alicloud-cs-auto-created'|head -1
```

Enable access through port 80 for the security group.

```
aliyun ecs AuthorizeSecurityGroup --RegionId cn-chengdu --SecurityGroupId ${Security group ID} --IpProtocol tcp --PortRange 80/80 --SourceCidrIp 0.0.0.0/0 --Priority 100
```

## Use WordPress

Enter the EIP that is retrieved in the preceding step in the address bar of a browser and press Enter to use WordPress.

