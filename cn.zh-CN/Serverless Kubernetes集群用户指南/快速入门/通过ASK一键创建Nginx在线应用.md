---
keyword: 一键创建Nginx在线应用
---

# 通过ASK一键创建Nginx在线应用

ASK集群无需管理节点，无需进行节点的安全维护等运维操作，满足您对应用托管的免运维诉求，让您关注在应用而非底层基础设施管理。本文介绍如何通过ASK部署在线Web应用，实现应用免运维托管。

[创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)

ASK支持标准Kubernetes的语义和API，您可以一键创建Deployment/StatefulSet/Jobs/Service/Ingress/CRD等资源，也可以使用Helm部署各种Kubernetes生态应用。

## 操作步骤

1.  [通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

2.  使用以下样例创建名为nginx.yaml的YAML文件。

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
    apiVersion: apps/v1beta2
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

3.  执行以下命令，部署Nginx示例应用。

    ```
    kubectl apply -f nginx.yaml
    ```

    预期输出：

    ```
    service/nginx-service created
    deployment.apps/nginx-deploy created
    ```

4.  查看Pod和Serivce状态，并通过SLB IP访问Nginx应用。

    1.  执行以下命令，查看Pod状态。

        ```
        kubectl get pod
        ```

        预期输出：

        ```
        nginx-deploy-55d8dcf755-bxk8n   1/1     Running   0          36s
        nginx-deploy-55d8dcf755-rchhq   1/1     Running   0          36s
        ```

    2.  执行以下命令，查看Serivce状态。

        ```
        kubectl get svc
        ```

        预期输出：

        ```
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
        kubernetes      ClusterIP      172.**.*.*     <none>        443/TCP        10d
        nginx-service   LoadBalancer   172.19.*.***   47.57.**.**   80:32278/TCP   39s
        ```

    3.  执行以下命令，通过SLB IP访问Nginx应用。

        ```
        curl 47.57.**.**
        ```

        预期输出：

        ```
        <!DOCTYPE html>
        <html>
        <head>
        <title>Welcome to nginx!</title>
        ...
        </html>
        ```

5.  扩容Deployment。

    1.  执行以下命令，查看Deployment。

        ```
        kubectl get deploy
        ```

        预期输出：

        ```
        NAME           READY   UP-TO-DATE   AVAILABLE   AGE
        nginx-deploy   2/2     2            2           9m32s
        ```

    2.  执行以下命令，扩容Deployment。

        ```
        kubectl scale deploy nginx-deploy --replicas=10
        ```

        预期输出：

        ```
        deployment.extensions/nginx-deploy scaled
        ```

    3.  执行以下命令，查看扩容后的Pod。

        ```
        kubectl get pod
        ```

        预期输出：

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

6.  配置HPA，可以让应用随着负载压力自动进行弹性扩容。当应用的CPU负载增高时，将水平扩容出更多的Pod副本。

    1.  执行以下命令，将应用副本缩容到1个。

        ```
        kubectl scale deploy nginx-deploy --replicas=1
        ```

        预期输出：

        ```
        deployment.extensions/nginx-deploy scaled
        ```

    2.  执行以下命令，查看Pod。

        ```
        kubectl get pod
        ```

        预期输出：

        ```
        NAME                            READY   STATUS    RESTARTS   AGE
        nginx-deploy-55d8dcf755-rchhq   1/1     Running   0          16m
        ```

    3.  执行以下命令，配置HPA。

        ```
        kubectl autoscale deployment nginx-deploy --cpu-percent=50 --min=1 --max=10
        ```

        预期输出：

        ```
        horizontalpodautoscaler.autoscaling/nginx-deploy autoscaled
        ```

    4.  执行以下命令，查看HPA。

        ```
        kubectl get hpa
        ```

        预期输出：

        ```
        NAME           REFERENCE                 TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
        nginx-deploy   Deployment/nginx-deploy   0%/30%    1         10        1          35s
        ```


