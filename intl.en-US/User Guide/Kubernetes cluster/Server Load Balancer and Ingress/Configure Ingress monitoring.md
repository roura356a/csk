# Configure Ingress monitoring {#concept_nmh_nks_vdb .concept}

You can view the Ingress monitoring data by enabling the default VTS module of Ingress.

## Enable VTS module by running commands {#section_chy_qqs_vdb .section}

1.  Modify the Ingress ConfigMap configuration to add the configuration item `enable-vts-status: "true"`.

    ```
    root@master # kubectl edit configmap nginx-configuration -n kube-system
    configmap "nginx-configuration" edited
    ```

    After the modification, the contents of the Ingress ConfigMap are as follows:

    ```
    apiVersion: v1
    data:
      enable-vts-status: "true" # Enable VTS module
      proxy-body-size: 20m
    kind: ConfigMap
    metadata:
      Annotations:
        kubectl.kubernetes.io/last-applied-configuration: |
          {"apiVersion":"v1","data":{"proxy-body-size":"20m"},"kind":"ConfigMap","metadata":{"annotations":{},"labels":{"app":"ingress-nginx"},"name":"nginx-configuration","namespace":"kube-system"}}
      creationTimestamp: 2018-03-20T07:10:18Z
      labels:
        app: ingress-nginx
      name: nginx-configuration
      namespace: kube-system
      selfLink: /api/v1/namespaces/kube-system/configmaps/nginx-configuration
    ```

2.  Verify if Ingress Nginx has enabled the VTS module normally.

    ```
    root@master # kubectl get pods --selector=app=ingress-nginx -n kube-system
    NAME READY STATUS RESTARTS AGE
    nginx-ingress-controller-79877595c8-78gq8 1/1 Running 0 1h
    root@master # kubectl exec -it nginx-ingress-controller-79877595c8-78gq8 -n kube-system -- cat /etc/nginx/nginx.conf | grep vhost_traffic_status_display
             vhost_traffic_status_display;
             vhost_traffic_status_display_format html;
    ```

3.  Locally access the Ingress Nginx monitoring console.

    **Note:** By default, the VTS port is not opened for security considerations. Here use the port-forward method to access the console.

    ```
    root@master # kubectl port-forward nginx-ingress-controller-79877595c8-78gq8 -n kube-system 18080
    Forwarding from 127.0.0.1:18080 -> 18080
    Handling connection for 18080
    ```

4.  Use `http://localhost:18080/nginx_status` to access the VTS monitoring console.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/153958494810733_en-US.png)


## Enable VTS module by using the Kubernetes dashboard {#section_g4b_sqs_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  On the Cluster List page of Kubernetes clusters, click **Dashboard** at the right of a cluster to enter the Kubernetes dashboard page.
3.  Select kube-system under Namespace in the left-side navigation pane. Click Config Maps in the left-side navigation pane. Click the icon at the right of nginx-configuration and then select View/edit YAML. Edit the config map to add the configuration item `enable-vts-status: "true"`.

    The contents of the saved Ingress ConfigMap are as follows:

    ```
    {
      "kind": "ConfigMap",
      "apiVersion": "v1",
      "metadata": {
        "name": "nginx-configuration",
        "namespace": "kube-system",
        "selfLink": "/api/v1/namespaces/kube-system/configmaps/nginx-configuration",
        "creationTimestamp": "2018-03-20T07:10:18Z",
        "labels": {
          "app": "ingress-nginx"
        },
        "annotations": {
          "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"data\":{\"proxy-body-size\":\"20m\"},\"kind\":\"ConfigMap\",\"metadata\":{\"annotations\":{},\"labels\":{\"app\":\"ingress-nginx\"},\"name\":\"nginx-configuration\",\"namespace\":\"kube-system\"}}\n"
       }
     },
     "data": {
       "proxy-body-size": "20m",
       "enable-vts-status": "true"
     }
    }
    ```

4.  Locally access the Ingress Nginx monitoring console.

    **Note:** By default, the VTS port is not opened for security considerations. Here use the port-forward method to access the console.

    ```
    root@master # kubectl port-forward nginx-ingress-controller-79877595c8-78gq8 -n kube-system 18080
    Forwarding from 127.0.0.1:18080 -> 18080
    Handling connection for 18080
    ```

5.  Use `http://localhost:18080/nginx_status` to access the VTS monitoring console.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/153958494810734_en-US.png)


