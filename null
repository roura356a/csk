---
keyword: [CronHPA, HPA, compatibility]
---

# How CronHPA interacts with HPA without conflicts

Cron Horizontal Pod Autoscaler \(CronHPA\) triggers horizontal scaling for containers based on predefined schedules. Horizontal Pod Autoscaler \(HPA\) is used to scale pods to ensure the availability of your applications when the network traffic spikes. If Container Service for Kubernetes \(ACK\) detects that both CronHPA and HPA are deployed, ACK sets the scaling object of CronHPA to the scaling object of HPA. This way, HPA can scale pods on behalf of CronHPA at the time scheduled by CronHPA. This topic describes how CronHPA interacts with HPA without conflicts.

## Conflicts between CronHPA and HPA

The following YAML template shows the configurations of CronHPA and HPA:



After you compare the configurations of CronHPA and HPA, you can find that:

-   The scaleTargetRef field is used in the configurations of both CronHPA and HPA to specify the object to be scaled.
-   The crontab rules in the jobs section of the CronHPA configuration specify the number of pods to be scaled when the scheduled time is reached.
-   HPA triggers scaling activities based on resource usage.

When CronHPA and HPA are both deployed, CronHPA and HPA may scale pods for the same application that is specified by scaleTargetRef. CronHPA and HPA are independent of each other and unaware of each other. As a result, the CronHPA controller and the HPA controller scale pods for the application separately. The later scaling activity overwrites the earlier one.

## Solution

The reason for the conflict between CronHPA and HPA is that the CronHPA controller and the HPA controller are unaware of each other. To resolve the conflict, CronHPA only needs to detect the state of HPA. In the HPA configuration, scaleTargetRef specifies the Deployment that is managed by HPA. The Deployment is related to a ReplicaSet. When HPA scales the Deployment, a number of pods that equals the value of ReplicaSet are scaled. ACK modifies the CronHPA configuration by setting scaleTargetRef to the object that is scaled by HPA. CronHPA can use this object to find the application that is specified by scaleTargetRef in the HPA configuration. This enables CronHPA to detect the state of HPA.

![cronhpa](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0532397061/p188680.png)

The following YAML template shows the configurations that enable CronHPA to interact with HPA without conflicts:

```
apiVersion: autoscaling.alibabacloud.com/v1beta1
kind: CronHorizontalPodAutoscaler
metadata:
  labels:
    controller-tools.k8s.io: "1.0"
  name: cronhpa-sample
spec:
   scaleTargetRef:
      apiVersion: autoscaling/v1
      kind: HorizontalPodAutoscaler
      name:  nginx-deployment-basic-hpa
   jobs:
   - name: "scale-down"
     schedule: "30 */1 * * * *"
     targetSize: 1
     runOnce: true
   - name: "scale-up"
     schedule: "0 */1 * * * *"
     targetSize: 3
     runOnce: true
```

After you deploy the preceding YAML template, CronHPA is aware of the values of minReplicas, maxReplicas, and desiredReplicas in the HPA configuration. CronHPA is also aware of the current number of pods provisioned for the application that is specified by scaleTargetRef. CronHPA can detect the state of HPA by modifying the configuration of HPA. CronHPA compares the desired number of pods with the current number of pods and then determines whether to trigger scaling activities and change the maximum number of pods in the HPA configuration. CronHPA also compares the desired number of pods with the maximum and minimum numbers of pods specified in the HPA configuration and then determines whether to change the minimum numbers of pods in the HPA configuration.

The following table describes the rules that enable CronHPA to interact with HPA without conflicts.

|HPA \(min/max\)|Cronhpa|Deployment|Scaling result|Description|
|---------------|-------|----------|--------------|-----------|
|1/10|5|5|-   HPA \(min/max\): 1/10
-   Deployment: 5

|If the number of pods desired by CronHPA equals the current number of pods, CronHAP does not change the maximum and minimum numbers of pods in the HPA configuration. In addition, no scaling activity is triggered.|
|1/10|4|5|-   HPA \(min/max\): 1/10
-   Deployment: 5

|If the number of pods desired by CronHPA is lower than the current number of pods, no scaling activity is triggered.|
|1/10|6|5|-   HPA \(min/max\): 6/10
-   Deployment: 6

|-   If the number of pods desired by CronHPA is greater than the current number of pods, CronHPA adds pods to reach the desired number.
-   If the number of pods desired by CronHPA is greater than the value of minReplicas in the HPA configuration, CronHPA changes the value of minReplicas. |
|5/10|4|5|-   HPA \(min/max\): 4/10
-   Deployment: 5

|-   If the number of pods desired by CronHPA is lower than the current number of pods, no scaling activity is triggered.
-   If the number of pods desired by CronHPA is lower than the value of minReplicas in the HPA configuration, CronHPA changes the value of minReplicas. |
|5/10|11|5|-   HPA \(min/max\): 11/11
-   Deployment: 11

|-   If the number of pods desired by CronHPA is greater than the current number of pods, CronHPA adds pods to reach the desired number.
-   If the number of pods desired by CronHPA is greater than the value of maxReplicas in the HPA configuration, CronHPA changes the value of maxReplicas. |

The following list describes the parameters in the table:

-   HPA \(min/max\): the minimum and maximum number of pods that are specified in HPA configuration.
-   CronHPA: the desired number of pods.
-   Deployment: the current number of pods that are provisioned for the application.

CronHPA does not directly change the number of pods for the Deployment. It triggers HPA to scale the pods. This resolves the conflict between CronHPA and HPA.

**Related topics**  


[Implement cron HPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Implement cron HPA.md)

[HPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/HPA.md)

