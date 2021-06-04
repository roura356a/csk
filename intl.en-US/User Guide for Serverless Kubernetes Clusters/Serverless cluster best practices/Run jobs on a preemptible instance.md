Run jobs on a preemptible instance 
=======================================================

You can run jobs or stateless applications on preemptible elastic container instances at reduced costs. This topic describes how to run jobs on a preemptible elastic container instance. 

Background information 
-------------------------------------------

Before you use a preemptible elastic container instance, take note of the following information:

* The market price of a preemptible instance fluctuates based on the supply and demand for its instance type. When you create a preemptible instance, you must specify a maximum hourly price to bid for a specific instance type. If your bid price is higher than the current market price and the resource inventory of the instance type is sufficient, the requested preemptible instance is created and billed at the current market price.

  

* A preemptible instance has a protection period of 1 hour after it is created. After the one-hour protection period of a preemptible instance expires, the instance is automatically released if the current market price of the preemptible instance exceeds the specified maximum hourly price or if resources are insufficient. A Kubernetes event occurs 3 minutes before a preemptible instance is released. During these 3 minutes, you can perform specific operations to prevent your services from being interrupted. For example, you can configure to deny inbound traffic to the instance.

  

* If a preemptible instance is released because the market price exceeds your bid price, the data on the instance is cleared, but the instance information is retained. In addition, the status of the instance changes to Failed and the cause of the failure is BidFailed. We recommend that you use persistent storage services such as cloud disks or Apsara File Storage NAS to store important data.

  




For more information, see [Overview](/intl.en-US/Instance/Instance purchasing options/Preemptible instances/Overview.md) and [Create a preemptible instance]().

Configuration methods 
------------------------------------------

You can configure annotations in the pod declaration to specify to create a preemptible elastic container instance. Take note of the following items:

* k8s.aliyun.com/eci-spot-strategy: specifies the bidding policy for the preemptible instance. Valid values:

  * SpotAsPriceGo: The current market price is automatically used as the bid price.

    
  
  * SpotWithPriceLimit: You must set the maximum hourly price that you are willing to pay for the preemptible instance.

    
  

  

* k8s.aliyun.com/eci-spot-price-limit: The maximum hourly price of the preemptible instance. This value can be accurate to three decimal places. You must specify thk8s.aliyun.com/eci-spot-price-limit when k8s.aliyun.com/eci-spot-strategy is set to SpotWithPriceLimit.

  



**Note**

When you create a preemptible elastic container instance, we recommend that you set k8s.aliyun.com/eci-use-specs to specify multiple ECS instance types to ensure that resources are sufficient to create the instance.

Example:

* SpotAsPriceGo

  In this mode, the system places bids based on the current market price. 

      apiVersion: v1
      kind: Pod
      metadata:
        name: spot-as-price-go
        annotations:
          k8s.aliyun.com/eci-use-specs: ecs.sn1ne.large,2-4Gi
          # If you set the k8s.aliyun.com/eci-spot-strategy annotation to SpotAsPriceGo, you do not need to specify a maximum hourly price. The instance is priced at the market price at the time of purchase.
          k8s.aliyun.com/eci-spot-strategy: SpotAsPriceGo

  

* SpotWithPriceLimit

  In this mode, the system places bids based on the specified maximum hourly price. The following situations may occur:
  * If your bid price is lower than the market price, the preemptible instance enters the Pending state. The system offers a bid price every 5 minutes. When your bid price is equal to or higher than the market price, the preemptible instance is created.

    
  
  * If your bid price is higher than or equal to the market price and resources are sufficient, your preemptible instance is created.

    
  

  

      apiVersion: v1
      kind: Pod
      metadata:
        name: spot-with-price-limit
        annotations:
          k8s.aliyun.com/eci-use-specs: ecs.sn1ne.large
          k8s.aliyun.com/eci-spot-strategy: SpotWithPriceLimit
          # If you set the k8s.aliyun.com/eci-spot-strategy annotation to SpotWithPriceLimit, you must specify a maximum hourly price. If your bid price is higher than the current pay-as-you-go price of the elastic container instance, the preemptible instance is created and billed on a pay-as-you-go basis. 
          k8s.aliyun.com/eci-spot-price-limit: "0.05"

  




Configuration examples 
-------------------------------------------

In Kubernetes, jobs are used to batch process short-lived and one-off tasks at a time. The following section provides an example of how to run jobs on a preemptible instance:

1. Prepare the configuration file of a job and name it spot_job.yaml. 

       apiVersion: batch/v1
       kind: Job
       metadata:
         name: pi
       spec:
         template:
           metadata:
             annotations:
               k8s.aliyun.com/eci-use-specs: ecs.t5-c1m2.large,2-4Gi
               k8s.aliyun.com/eci-spot-strategy: SpotAsPriceGo    #The current market price is automatically used as the bid price.
           spec:
             containers:
             - name: pi
               image: registry-vpc.cn-beijing.aliyuncs.com/ostest/perl
               command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
             restartPolicy: Never

   

2. Create a job. 

       kubectl create -f spot_job.yaml

   

3. View the running status. 

       kubectl get pod

   

   The command output shows that the corresponding pod is created. Example output:

       NAME       READY   STATUS      RESTARTS   AGE
       pi-frmr8   1/1     Running     0          5s

   




A preemptible instance has a protection period of 1 hour after it is created. During this period, the instance is not released even if your bid price is lower than the current market price. This ensures that business can run properly on the instance. After the one-hour protection period of a preemptible instance expires, the instance is automatically released if the current market price of the preemptible instance exceeds the specified maximum hourly price or if resources are insufficient. You are notified of the release in the following ways:

* Event notifications before release

  Elastic Container Instance sends a release event to the list of Kubernetes events 3 minutes before the instance is released. You can run the following commands to view the event:
  * View pod details

        kubectl describe pod pi-frmr8

    

    Information about the release event is displayed in the Events section of the command output. Example output:

        Events:
          Type     Reason            Age    From          Message
          ----     ------            ----   ----          -------
          Warning  SpotToBeReleased  3m32s  kubelet, eci  Spot ECI will be released in 3 minutes

    
  
  * View event details

        kukubectl get events

    

    Information about the release event is displayed in the command output. Example output:

        LAST SEEN   TYPE      REASON             OBJECT         MESSAGE
        3m39s       Warning   SpotToBeReleased   pod/pi-frmr8   Spot ECI will be released in 3 minutes

    
  

  

* Status displayed after release

  After a preemptible instance is released, the instance information is still retained. The status of the instance is changed to Failed and the cause of the failure is BidFailed. You can run the following commands to view the instance status:
  * View pod information

        kubectl get pod

    

    The status of the pod is displayed as BidFailed in the command output. Example output:

        NAME       READY   STATUS      RESTARTS   AGE
        pi-frmr8   1/1     BidFailed   0          3h5m

    
  
  * View pod details

        kubectl describe pod pi-frmr8

    

    The status of the pod is displayed as Failed, and the reason is displayed as BidFailed in the command output. Example output:

        Status:             Failed
        Reason:             BidFailed
        Message:            The pod is spot instance, and have been released at 2020-04-08T12:36Z

    
  

  




After the instance is released, Job Controller in Kubernetes creates new instances to run jobs. You can run the `kubectl get pod` command to view the pod. Example output:

    NAME       READY   STATUS      RESTARTS   AGE
    pi-frmr8   1/1     BidFailed   0          4h53m
    pi-kp5zx   1/1     Running     0          3h45m


