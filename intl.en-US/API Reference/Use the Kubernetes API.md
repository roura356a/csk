# Use the Kubernetes API

This topic describes how to call the Kubernetes API by using cURL to make REST calls. This allows you to send HTTPS requests to manage Kubernetes clusters. The following examples demonstrate how to create and delete pods, and how to create and modify Deployments.

## Obtain cluster credentials in the kubeconfig file

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the cluster details page, click the **Connection Information** tab. You can find the cluster credential in the **kubeconfig** file. Copy the content of the **kubeconfig** file and save it to your on-premises machine.

5.  Run the following commands to retrieve information about the certificate authority \(CA\), the private key, and the API server:

    ```
    cat  ./kubeconfig |grep client-certificate-data | awk -F ' ' '{print $2}' |base64 -d > ./client-cert.pem
    cat  ./kubeconfig |grep client-key-data | awk -F ' ' '{print $2}' |base64 -d > ./client-key.pem
    APISERVER=`cat  ./kubeconfig |grep server | awk -F ' ' '{print $2}'`
    ```


## Use cURL to call the Kubernetes API

Run the following command to query all namespaces in the cluster:

```
curl --cert ./client-cert.pem --key ./client-key.pem -k $APISERVER/api/v1/namespaces
```

-   Common operations to manage pods

    Run the following command to query all pods in the default namespace:

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/api/v1/namespaces/default/pods
    ```

    Run the following command to create a pod \(JSON format\):

    ```
    cat nginx-pod.json
    {
        "apiVersion":"v1",
        "kind":"Pod",
        "metadata":{
            "name":"nginx",
            "namespace": "default"
        },
        "spec":{
            "containers":[
                {
                    "name":"ngnix",
                    "image":"nginx:alpine",
                    "ports":[
                        {
                            "containerPort": 80
                        }
                    ]
                }
            ]
        }
    }
    
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/api/v1/namespaces/default/pods -X POST --header 'content-type: application/json' -d@nginx-pod.json
    ```

    Run the following command to create a pod \(YAML format\):

    ```
    cat nginx-pod.yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
      namespace: default
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80
    
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/api/v1/namespaces/default/pods -X POST --header 'content-type: application/yaml' --data-binary @nginx-pod.yaml
    ```

    Run the following command to query the status of a pod:

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/api/v1/namespaces/default/pods/nginx
    ```

    Run the following command to query the log data of a pod:

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/api/v1/namespaces/default/pods/nginx/log
    ```

    Run the following command to collect metrics of a pod by using the metric-server API:

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/apis/metrics.k8s.io/v1beta1/namespaces/default/pods/nginx
    ```

    Run the following command to delete a pod:

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/api/v1/namespaces/default/pods/nginx -X DELETE
    ```

-   Common operations to manage Deployments

    Create a Deployment. The following YAML template is an example:

    ```
    cat nginx-deploy.yaml
    apiVersion: apps/v1
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
    
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/apis/apps/v1/namespaces/default/deployments -X POST --header 'content-type: application/yaml' --data-binary @nginx-deploy.yaml
    ```

    Run the following command to query a Deployment:

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/apis/apps/v1/namespaces/default/deployments
    ```

    Run the following command to update a Deployment by modifying the number of replicated pods:

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/apis/apps/v1/namespaces/default/deployments/nginx-deploy -X PATCH -H 'Content-Type: application/strategic-merge-patch+json' -d '{"spec": {"replicas": 4}}'
    ```

    Run the following command to update a Deployment by changing the container image:

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/apis/apps/v1/namespaces/default/deployments/nginx-deploy -X PATCH -H 'Content-Type: application/strategic-merge-patch+json' -d '{"spec": {"template": {"spec": {"containers": [{"name": "nginx","image": "nginx:1.7.9"}]}}}}'
    ```


**Related topics**  


[Open source SDKs](https://kubernetes.io/docs/reference/using-api/client-libraries/)

[Kubernetes API specifications](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/)

[Other methods to access Kubernetes clusters](https://kubernetes.io/docs/tasks/administer-cluster/access-cluster-api/)

We recommend that you use SDKs of open source Kubernetes. Supported programming languages include Go, Python, and Java.

  


