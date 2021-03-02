# FAQ

This topic provides answers to commonly asked questions about network management.

-   [How can I rename SLB instances if the Cloud Controller Manager \(CCM\) version is V1.9.3.10 or earlier?](#section_30n_419_ho6)
-   [Which network plug-in do I need to choose for an ACK cluster, Terway or Flannel?](#section_six_gw0_14t)
-   [How do I manually install alicloud-application-controller?](#section_91h_eqi_dck)
-   [Can I use hostPorts to create port mappings in an ACK cluster?](#section_ge8_5r0_zcj)
-   [How does CCM calculate node weights in Local mode?](#section_wet_85s_5fm)

## How can I rename SLB instances if the Cloud Controller Manager \(CCM\) version is V1.9.3.10 or earlier?

For CCM V1.9.3.10 and earlier, you must manually add tags to Server Load Balancer \(SLB\) instances. For CCM versions later than V1.9.3.10, tags are automatically added to SLB instances in the cluster. This way, you can rename SLB instances. Perform the following steps:

**Note:**

-   You can rename SLB instances by adding tags to the instances only if the CCM version is V1.9.3.10 or earlier.
-   The service type is LoadBalancer.

1.  Log on to a master node in a Container Service for Kubernetes \(ACK\) cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

2.  Run the `# kubectl get svc -n ${namespace} ${service}` command to view the service type and service IP address.

    ![Service type](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3545359951/p44550.png)

    **Note:** Replace $\{namespace\} and $\{service\} with the cluster namespace and service name.

3.  Run the following command to generate a tag for the SLB instance:

    ```
    kubectl get svc -n ${namespace} ${service} -o jsonpath="{.metadata.uid}"|awk -F "-" '{print "kubernetes.do.not.delete: "substr("a"$1$2$3$4$5,1,32)}'
    ```

    ![tag](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3545359951/p44551.png)

4.  Log on to the [SLB console](https://slb.console.aliyun.com), select the region where the SLB instance is deployed, and then use the IP address that is returned in Step 2 to find the specified SLB instance.

5.  Add the tag that is generated in Step 3 to the SLB instance. Callout 1 in the preceding figure shows the tag key, and callout 2 shows the tag value. For more information, see [Add a tag](/intl.en-US/Classic Load Balancer/User Guide/Instance/Tags/Tag overview.md).


## Which network plug-in do I need to choose for an ACK cluster, Terway or Flannel?

Two network plug-ins are used to create an ACK cluster: Flannel and Terway.

You can select a network plug-in based on the following descriptions to meet your business requirements:

-   Flannel: a simple and stable Container Network Interface \(CNI\) plug-in developed by the Kubernetes community. You can use [Flannel](https://github.com/coreos/flannel) with Virtual Private Cloud \(VPC\) of Alibaba Cloud. This enables your clusters and containers to run in a high-speed and stable network. Flannel does not support standard Kubernetes network policies.
-   Terway: a network plug-in developed by Alibaba Cloud Container Service. Terway is fully compatible with Flannel and allows you to assign Alibaba Cloud Elastic Network Interfaces \(ENIs\) to containers. It also allows you to customize network policies of Kubernetes to control intercommunication among containers. Terway supports bandwidth throttling on individual containers. If you do not need to use network policies, select Flannel. In other cases, we recommend that you select Terway. For more information about Terway, see [Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Container network/Work with Terway.md).

## How do I manually install alicloud-application-controller?

For Kubernetes V1.10.4 and later, alicloud-application-controller is automatically installed to enable phased releases based on custom resource definitions \(CRDs\).

**Note:** For the latest Kubernetes version, alicloud-application-controller is automatically installed. For Kubernetes versions later than V1.9.3 and earlier than V1.10.4, you must manually install alicloud-application-controller.

You can run the `kubectl create -f alicloud-application-controller.yml` command to install alicloud-application-controller. Copy the following content to the alicloud-application-controller.yml file:

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: alicloud-application-controller
  labels:
    owner: aliyun
    app: alicloud-application-controller
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      owner: aliyun
      app: alicloud-application-controller
  template:
    metadata:
      labels:
        owner: aliyun
        app: alicloud-application-controller
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ''
    spec:
      tolerations:
      - effect: NoSchedule
        operator: Exists
        key: node-role.kubernetes.io/master
      - effect: NoSchedule
        operator: Exists
        key: node.cloudprovider.kubernetes.io/uninitialized
      containers:
        - name: alicloud-application-controller
          image: registry.cn-hangzhou.aliyuncs.com/acs/aliyun-app-lifecycle-manager:0.1-c8d5da8
          imagePullPolicy: IfNotPresent
      serviceAccount: admin
```

## Can I use hostPorts to create port mappings in an ACK cluster?

-   No, you cannot use hostPorts to create port mappings in an ACK cluster.
-   A pod in a VPC can be accessed by other cloud resources deployed in the same VPC through the endpoint of the pod in the VPC. Therefore, port mapping is not required.
-   If the service type is NodePort or LoadBalancer, you can make a service accessible over the Internet.

## How does CCM calculate node weights in Local mode?

Assume that pods with the app=nginx label are deployed on three Elastic Compute Service \(ECS\) instances. In the following figure, when externalTrafficPolicy is set to Local, the pods provide services for external users by using Service A. The following sections describe how node weights are calculated.

![CCM2](../images/p103113.png "")

-   **For CCM V1.9.3.164-g2105d2e-aliyun and later**

    Assume that the CCM version is earlier than V1.9.3.164-g2105d2e-aliyun. The following figure shows that the weight of each ECS instance in Local mode is 100. This indicates that traffic loads are evenly distributed to the ECS instances. However, the loads of the pods are different because the pods are unevenly deployed on the ECS instances. For example, the pod on ECS 1 has the heaviest load and the pods on ECS 3 have the lightest load.

    ![CCM3](../images/p103116.png "")

-   **For CCM versions later than V1.9.3.164-g2105d2e-aliyun and earlier than V1.9.3.276-g372aa98-aliyun**

    For CCM versions later than V1.9.3.276-g372aa98-aliyun and earlier than V1.9.3.164-g2105d2e-aliyun, the node weights are calculated based on the number of pods deployed on each node. The weights of the ECS instances are 16, 33, and 50. Therefore, traffic loads are distributed to the ECS instances at the ratio of 1:2:3.

    Calculation formula:

    ![Calculation formula](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4545359951/p103112.png)

    ![ccm4](../images/p103117.png "")

-   **For CCM V1.9.3.276-g372aa98-aliyun and later**

    The weight calculation in the preceding figure still has a slight imbalance due to the precision of the calculation formula. For CCM V1.9.3.276-g372aa98-aliyun and later, the weight of each node equals the number of pods deployed on the node. In the following figure, the weights of the ECS instances are 1, 2, and 3. Traffic loads are distributed to the ECS instances at the ratio of 1:2:3. The pods have a more balanced load than those in the preceding figure.

    Calculation formula:

    ![Node weight](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4545359951/p118174.png)

    ![node](../images/p118175.png "")


