# Update Istio

A Kubernetes controller is used for Istio lifecycle management. You can **update** the Istio configurations in the Container Service for Kubernetes \(ACK\) console. Modifications to the deployed YAML file of Istio are overwritten by the operations in the ACK console.

-   [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md)
-   [Deploy Istio](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Deploy Istio.md)

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  Log on to the ACK console. In the left-side navigation pane, choose **Service Mesh** \> **Istio Management**.

3.  Click **Update** in the upper-right corner.

4.  On the Update Release page, set the parameters and click **Update**.

    |Parameter|Description|
    |---------|-----------|
    |certManager.enabled|Specifies whether to enable cert-manager. Valid values: true and false.|
    |    ```
galley:    
  resources:    
    requests:      
      cpu: 200m      
      memory: 256Mi    
    limits:      
      cpu: 2000m      
      memory: 2G    
  affinity: {}  
  replicaCount: 1  
  tolerations: [] 
    ```

|Galley parameters, which include resource requests, resource limits, replica quantity, affinity, and tolerations.|
    |    ```
gateways:
  enabled: false  
  ingress:  
  - enabled: true    
    gatewayName: ingressgateway    
    maxReplicas: 2    
    minReplicas: 1 
....
    ```

|Ingress gateway parameters, which include resource requests, resource limits, replica quantity, affinity, and tolerations.|
    |    ```
grafana:
  enabled: true
    ```

|Grafana parameters, which include resource requests, resource limits, replica quantity, affinity, and tolerations.|
    |    ```
kiali:
  enabled: true
    ```

|Kiali parameters, which include resource requests, resource limits, replica quantity, affinity, and tolerations.|
    |    ```
pilot:
                                        
    ```

|Pilot parameters, which include resource requests, resource limits, replica quantity, affinity, and tolerations.|
    |    ```
prometheus:  
  enabled: true
    ```

|Prometheus parameters, which include resource requests, resource limits, replica quantity, affinity, and tolerations.|
    |    ```
tracingOnExtZipkin:  
  enabled: false  
  externalZipkin: 
    endpoint: ""  
    ```

|Parameters for the Tracing Analysis service. To configure this option, activate Tracing Analysis and specify the endpoint of the Tracing Analysis service. For example, you can specify http://tracing-analysis-dc-hz.aliyuncs.com/.../api/v1/spans as the endpoint. This is a public endpoint or an internal endpoint of the Zipkin API V1. After you enable the Tracing Analysis service, a Zipkin client uses the endpoint to transmit collected data to the Tracing Analysis service.|


In the left-side navigation pane of the cluster details page, click **Workload**. On the **Pods** tab, select the cluster and the namespace where Istio is updated. Then, you can view information about the updated Istio.

**Related topics**  


[Delete Istio](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Delete Istio.md)

