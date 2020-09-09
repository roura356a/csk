# Create ECIs across zones

The system may exhaust the instances and VSwitch IP resources in a zone when it attempts to create sufficient instances to handle traffic spikes or process a large number of jobs in parallel. This also applies to when you need to run a large number of instances in parallel to process jobs. In this case, you can deploy a Serverless Kubernetes \(ASK\) cluster across multiple zones to prevent resource shortage.

-   ECI distributes the requests for creating ECIs to all VSwitches to balance the heavy load.
-   If a zone attached to a VSwitch does not have sufficient resources to create the required ECIs, the system selects another VSwitch.

## Procedure

1.  When you create an ASK cluster, you can select an existing Virtual Private Cloud \(VPC\) network and select multiple VSwitches in the VPC to specify multiple zones. For more information about how to create a cluster, see [Create a serverless Kubernetes cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create a serverless Kubernetes cluster.md).

    ![Cross-zone ASK](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0297297951/p103604.png)

2.  Modify the configuration of zones for a cluster.

    After a cluster is created, modify the VSwitch configuration for the cluster. You do not need to recreate the cluster. Modify the `vswitch` field in `kube-system/eci-profile configmap` and the modification immediately takes effect.

    ```
    kubectl -n kube-system get cm eci-profile -o yaml
    ```

    ```
    apiVersion: v1
    data:
      kube-proxy: "true"
      privatezone: "true"
      quota-cpu: "192000"
      quota-memory: 640Ti
      quota-pods: "4000"
      region: cn-hangzhou
      resourcegroup: ""
      securitygroup: sg-xxx
      vpc: vpc-xxx
      vswitch: vsw-xxx,vsw-yyy,vsw-zzz
    kind: ConfigMap
    ```


