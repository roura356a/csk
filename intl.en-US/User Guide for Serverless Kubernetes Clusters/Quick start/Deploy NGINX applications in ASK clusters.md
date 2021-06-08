---
keyword: deploy NGINX applications in ASK clusters
---

# Deploy NGINX applications in ASK clusters

You do not need to create or manage nodes in serverless Kubernetes \(ASK\) clusters. This frees you from maintaining cloud resources for your applications and allows you to focus on your business development instead of managing the underlying infrastructure. This topic describes how to deploy web applications in ASK clusters.

[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)

ASK clusters support standard Kubernetes semantics and API operations. You can create cluster resources with a few clicks, such as Deployments, StatefulSets, Jobs, Services, Ingresses, and custom resources. You can also use Helm to deploy a variety of Kubernetes-native applications in ASK clusters.

## Procedure

1.  [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

2.  Create a YAML file named nginx.yaml and copy the following content to the file:

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-service
    spec:
      ports:
      - port: 80
        protocol: TCP
      selector:
        app: nginx
      type: LoadBalancer
    ---
    apiVersion: apps/v1  #Specify the version based on the Kubernetes version that you use. 
    kind: Deployment
    metadata:
      name: nginx-deploy
      labels:
        app: nginx
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image:  nginx:alpine
            ports:
            - containerPort: 80
            resources:
              requests:
                cpu: "2"
                memory: "4Gi"
    ```

    **Note:** The relationships between Kubernetes versions and the apiVersion of Deployments:

    -   Kubernetes versions earlier than 1.6: `extensions/v1beta1`.
    -   Kubernetes 1.6 to 1.9: `apps/v1beta1`.
    -   Kubernetes versions later than 1.9: `apps/v1`.
3.  Run the following command to deploy an NGINX application in the ASK cluster:

    ```
    kubectl apply -f nginx.yaml
    ```

    Expected output:

    ```
    service/nginx-service created
    deployment.apps/nginx-deploy created
    ```

4.  Check the states of the deployed pods and Services. You can use the IP address of the created Server Load Balancer \(SLB\) instance to access the NGINX application.

    1.  Run the following command to query the states of the pods that are provisioned for the application:

        ```
        kubectl get pod
        ```

        Expected output:

        ```
        nginx-deploy-55d8dcf755-bxk8n   1/1     Running   0          36s
        nginx-deploy-55d8dcf755-rchhq   1/1     Running   0          36s
        ```

    2.  Run the following command to query the states of the created Services:

        ```
        kubectl get svc
        ```

        Expected output:

        ```
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
        kubernetes      ClusterIP      172.**.*.*     <none>        443/TCP        10d
        nginx-service   LoadBalancer   172.19.*.***   47.57.**.**   80:32278/TCP   39s
        ```

    3.  Run the following command to use the IP address of the created SLB instance to access the NGINX application:

        ```
        curl 47.57.**.**
        ```

        Expected output:

        ```
        <!DOCTYPE html>
        <html>
        <head>
        <title>Welcome to nginx!</title>
        ...
        </html>
        ```

5.  Scale out the created Deployment.

    1.  Run the following command to query the created Deployment:

        ```
        kubectl get deploy
        ```

        Expected output:

        ```
        NAME           READY   UP-TO-DATE   AVAILABLE   AGE
        nginx-deploy   2/2     2            2           9m32s
        ```

    2.  Run the following command to scale out the created Deployment:

        ```
        kubectl scale deploy nginx-deploy --replicas=10
        ```

        Expected output:

        ```
        deployment.extensions/nginx-deploy scaled
        ```

    3.  Run the following command to query the pods after the Deployment is scaled out:

        ```
        kubectl get pod
        ```

        Expected output:

        ```
        NAME                            READY   STATUS    RESTARTS   AGE
        nginx-deploy-55d8dcf755-8jlz2   1/1     Running   0          39s
        nginx-deploy-55d8dcf755-9jbzk   1/1     Running   0          39s
        nginx-deploy-55d8dcf755-bqhcz   1/1     Running   0          38s
        nginx-deploy-55d8dcf755-bxk8n   1/1     Running   0          10m
        nginx-deploy-55d8dcf755-cn6x9   1/1     Running   0          38s
        nginx-deploy-55d8dcf755-jsqjn   1/1     Running   0          38s
        nginx-deploy-55d8dcf755-lhp8l   1/1     Running   0          38s
        nginx-deploy-55d8dcf755-r2clb   1/1     Running   0          38s
        nginx-deploy-55d8dcf755-rchhq   1/1     Running   0          10m
        nginx-deploy-55d8dcf755-xspnt   1/1     Running   0          38s
        ```

6.  You can use Horizontal Pod Autoscaler \(HPA\) to automatically scale out pods when the CPU usage exceeds the scale-out threshold. After HPA is enabled, pods are added for the Deployment when the CPU usage exceeds the scale-out threshold.

    1.  Run the following command to scale in the number of pods to one for the Deployment:

        ```
        kubectl scale deploy nginx-deploy --replicas=1
        ```

        Expected output:

        ```
        deployment.extensions/nginx-deploy scaled
        ```

    2.  Run the following command to query the states of the pod:

        ```
        kubectl get pod
        ```

        Expected output:

        ```
        NAME                            READY   STATUS    RESTARTS   AGE
        nginx-deploy-55d8dcf755-rchhq   1/1     Running   0          16m
        ```

    3.  Run the following command to configure HPA:

        ```
        kubectl autoscale deployment nginx-deploy --cpu-percent=50 --min=1 --max=10
        ```

        Expected output:

        ```
        horizontalpodautoscaler.autoscaling/nginx-deploy autoscaled
        ```

    4.  Run the following command to query HPA:

        ```
        kubectl get hpa
        ```

        Expected output:

        ```
        NAME           REFERENCE                 TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
        nginx-deploy   Deployment/nginx-deploy   0%/30%    1         10        1          35s
        ```


