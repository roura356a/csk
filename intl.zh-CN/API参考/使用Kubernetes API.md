# 使用Kubernetes API

本文将基于cURL命令简单演示如何以REST的方式使用Kubernetes API，方便您使用开发语言原生的HTTPS方式操作Kubernetes集群。演示包括创建和删除Pod，创建和修改Deployment。

## 获取集群访问凭证kubeconfig

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  单击**连接信息**页签，查看集群访问凭证（**kubeconfig**），将**kubeconfig**文件保存到本地。

5.  从kubeconfig文件中提取ca、key和apiserver信息，命令如下。

    ```
    cat  ./kubeconfig |grep client-certificate-data | awk -F ' ' '{print $2}' |base64 -d > ./client-cert.pem
    cat  ./kubeconfig |grep client-key-data | awk -F ' ' '{print $2}' |base64 -d > ./client-key.pem
    APISERVER=`cat  ./kubeconfig |grep server | awk -F ' ' '{print $2}'`
    ```


## 使用cURL命令操作Kubernetes API

执行以下命令查看当前集群中所有Namespaces。

```
curl --cert ./client-cert.pem --key ./client-key.pem -k $APISERVER/api/v1/namespaces
```

-   常用的Pod相关操作。

    执行以下命令查看default命名空间下的所有Pods。

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/api/v1/namespaces/default/pods
    ```

    执行以下命令创建Pod（JSON格式）。

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

    执行以下命令创建Pod（YAML格式）。

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

    执行以下命令查询Pod状态。

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/api/v1/namespaces/default/pods/nginx
    ```

    执行以下命令查询Pod logs。

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/api/v1/namespaces/default/pods/nginx/log
    ```

    执行以下命令查询Pod的metrics数据（通过metric-server api）。

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/apis/metrics.k8s.io/v1beta1/namespaces/default/pods/nginx
    ```

    执行以下命令删除Pod。

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/api/v1/namespaces/default/pods/nginx -X DELETE
    ```

-   常用的Deployment相关操作。

    创建Deployment示例YAML文件如下。

    ```
    cat nginx-deploy.yaml
    apiVersion: extensions/v1beta1
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
    
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/apis/extensions/v1beta1/namespaces/default/deployments -X POST --header 'content-type: application/yaml' --data-binary @nginx-deploy.yaml
    ```

    执行以下命令查看Deployment。

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/apis/extensions/v1beta1/namespaces/default/deployments
    ```

    执行以下命令更新Deployment（修改replicas副本数量）。

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/apis/extensions/v1beta1/namespaces/default/deployments/nginx-deploy -X PATCH -H 'Content-Type: application/strategic-merge-patch+json' -d '{"spec": {"replicas": 4}}'
    ```

    执行以下命令更新Deployment（修改容器镜像）。

    ```
    curl --cert client-cert.pem --key client-key.pem -k $APISERVER/apis/extensions/v1beta1/namespaces/default/deployments/nginx-deploy -X PATCH -H 'Content-Type: application/strategic-merge-patch+json' -d '{"spec": {"template": {"spec": {"containers": [{"name": "nginx","image": "nginx:1.7.9"}]}}}}'
    ```


**相关文档**  


[官方SDK](https://kubernetes.io/docs/reference/using-api/client-libraries/)

[详细的Kubernetes API规范](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/)

[其他Kubernetes集群访问方式](https://kubernetes.io/docs/tasks/administer-cluster/access-cluster-api/)

推荐您使用Kubernetes官方维护的SDK（包括Go、Python、Java等语言）。

  


