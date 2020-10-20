# Use a preemptible instance to run a job

This topic describes how to use a preemptible instance of Elastic Container Instance \(ECI\) to run a job in a serverless Kubernetes cluster.

## Background

You can effectively reduce the costs of using ECI by using preemptible instances. Preemptible instances are especially suitable for running short-term jobs or deployments.

1.  The market price of a preemptible instance fluctuates based on changes to the demand for and supply of its instance type. Compared with a pay-as-you-go instance, the costs of using a preemptible instance are often lower.
2.  A preemptible instance has a guaranteed period of one hour after it is created. During this period, ECI cannot release the instance. After the protection period ends, ECI may release the instance at any time. About 3 minutes before the release, ECI notifies you of the release through a Kubernetes event. During this three-minute period, you can manage services to ensure that they are not affected by the release of the instance. For example, you can disable the inbound traffic to the instance.
3.  If a preemptible instance is released because your bid is lower than the market price, the data on the instance is also released, but the information about the instance is retained. In addition, the instance enters the Failed state and the failure cause is BidFailed. We recommend that you use persistent storage services such as Block Storage and Apsara File Storage NAS to store important data so that it will not be lost when a preemptible instance is released.

For more information, see [Use preemptible instances]() and [Billing]().

## Create a preemptible instance

You can add the annotations listed in the following table to the declaration of an ECI to create a preemptible instance.

|Annotation|Type|Example|Description|
|----------|----|-------|-----------|
|k8s.aliyun.com/eci-spot-strategy|String|SpotAsPriceGo|The type of the preemptible instance. Valid values: **SpotAsPriceGo** and **SpotWithPriceLimit**. A value of SpotAsPriceGo specifies a preemptible instance priced at the market price at the time of purchase. A value of SpotWithPriceLimit specifies a preemptible instance with a maximum hourly price.|
|k8s.aliyun.com/eci-spot-price-limit|Float|"0.250"|The maximum hourly price that you accept. This annotation only takes effect when the k8s.aliyun.com/eci-spot-strategy annotation is set to SpotWithPriceLimit. A maximum of three decimal places can be specified.|

**Note:**

When you create a preemptible instance, you can use the `k8s.aliyun.com/eci-use-specs` annotation to specify **one or more instance types**. This can effectively improve the success rate of creating the preemptible instance. For more information, see [Pod annotations supported by ECI]().

## SpotAsPriceGo

Specifies a preemptible instance priced at the market price at the time of purchase.

```
apiVersion: v1
kind: Pod
metadata:
  name: spot-as-price-go
  annotations:
    k8s.aliyun.com/eci-use-specs: ecs.sn1ne.large,2-4Gi
    # If you set the k8s.aliyun.com/eci-spot-strategy annotation to SpotAsPriceGo, you do not need to specify the maximum hourly price. The instance is priced at the market price at the time of purchase.
    k8s.aliyun.com/eci-spot-strategy: SpotAsPriceGo
```

## SpotWithPriceLimit

Specifies a preemptible instance with a maximum hourly price. The system automatically offers a bid for you based on the maximum hourly price. The bidding involves the following situations:

1.  Your bid is lower than the market price. In this case, the preemptible instance enters the Pending state. The system offers a bid every 5 minutes. When your bid is equal to or higher than the market price, the preemptible instance is created. After the preemptible instance runs for 1 hour, if your bid is lower than the market price, the preemptible instance may be released at any time.
2.  Your bid is equal to or higher than the market price. In this case, the preemptible instance is created if resources are sufficient. The preemptible instance is priced at the market price at the time of purchase.The default market price is an hourly price.You can divide the market price by 3,600 to obtain the price per second. Preemptible instances are billed by the second.

```
apiVersion: v1
kind: Pod
metadata:
  name: spot-with-price-limit
  annotations:
    k8s.aliyun.com/eci-use-specs: ecs.sn1ne.large
    k8s.aliyun.com/eci-spot-strategy: SpotWithPriceLimit
    # If you set the k8s.aliyun.com/eci-spot-strategy annotation to SpotWithPriceLimit, you must specify a maximum hourly price.
    # If your bid is higher than the price of a pay-as-you-go instance, the preemptible instance is created and priced as a pay-as-you-go instance.
    k8s.aliyun.com/eci-spot-price-limit: "0.05"
```

## Example

Create a job to be run on a preemptible instance that is priced at the market price at the time of purchase.

```
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    metadata:
      annotations:
        k8s.aliyun.com/eci-use-specs: ecs.t5-c1m2.large,2-4Gi
        k8s.aliyun.com/eci-spot-strategy: SpotAsPriceGo
    spec:
      containers:
      - name: pi
        image: registry-vpc.cn-beijing.aliyuncs.com/ostest/perl
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
```

Submit the job.

```
kubectl create -f spot_job.yaml
kubectl get po
```

```
NAME       READY   STATUS      RESTARTS   AGE
pi-frmr8   1/1     Running     0          5s
```

## Notification of instance release

A preemptible instance has a guaranteed period of one hour after it is created. During this period, the instance is not released even if your bid is lower than the current market price, which ensures that the instance can run services normally. After the guaranteed period ends, the preemptible instance is released if your bid is lower than the market price or resources are insufficient. ECI notifies you of the release in the following ways:

-   ECI sends a release event to the list of Kubernetes events 3 minutes before the release of the instance. You can run the `kubectl describe pod ${pod_name}` command to view the event.

```
kubectl describe po pi-frmr8
```

```
......
Events:
  Type     Reason            Age    From          Message
  ----     ------            ----   ----          -------
  Warning  SpotToBeReleased  3m32s  kubelet, eci  Spot ECI will be released in 3 minutes
```

```
kubectl get events
```

```
LAST SEEN   TYPE      REASON             OBJECT         MESSAGE
3m39s       Warning   SpotToBeReleased   pod/pi-frmr8   Spot ECI will be released in 3 minutes
```

-   After the instance is released, ECI changes the status of the instance to `Failed` and sets the failure cause to `BidFailed`. You can view the status of the instance in the instance details.

```
kubectl get po
```

```
NAME       READY   STATUS      RESTARTS   AGE
pi-frmr8   1/1     BidFailed   0          3h5m
```

```
kubectl describe po pi-frmr8
```

```
......
Status:             Failed
Reason:             BidFailed
Message:            The pod is spot instance, and have been released at 2020-04-08T12:36Z
......
```

After the instance is released, the job controller or deployment controller in Kubernetes creates a new instance to continue running the job.

```
kubectl get po
```

```
NAME       READY   STATUS      RESTARTS   AGE
pi-frmr8   1/1     BidFailed   0          4h53m
pi-kp5zx   1/1     Running     0          3h45m
```

