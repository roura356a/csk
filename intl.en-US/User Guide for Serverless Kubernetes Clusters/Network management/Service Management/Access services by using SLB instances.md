# Access services by using SLB instances

After you create a LoadBalancer service, you can access the service by using a Server Load Balancer \(SLB\) instance. For access requests from outside the cluster, you can use the domain name of the SLB instance and the service port, or use the IP address of the SLB instance and the service port. For access requests from within the cluster, you can use the service name and the service port. This topic describes how to access services by using SLB instances.

You have read the considerations described in [Considerations for configuring a LoadBalancer type Service](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Considerations for configuring a LoadBalancer type Service.md).

Assume that the Cloud Controller Manager \(CCM\) version of your cluster is 1.9.3 or later, and you have specified an existing SLB instance for a service. By default, CCM does not configure listeners for the SLB instance. You can use the annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners: "true"` to enable CCM to configure listeners for the SLB instance. You can also manually configure listeners for the SLB instance.

You can use the following methods to view the CCM version:

-   View the CCM version in the Container Service for Kubernetes \(ACK\) console.
    1.  Log on to the [the ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the specific cluster, and choose **More** \> **Manage System Components** in the Actions column of the cluster. The Core Components tab appears on the **Add-ons** page. You can view the CCM version on the Core Components tab.
-   Run the following command to query the CCM version. This method is applicable to dedicated clusters only.

    ```
    kubectl get pod -n kube-system -o yaml|grep image:|grep cloud-con|uniq
    ```


## Use the command-line tool

**Method 1:**

1.  Use kubectl to create an NGINX service.

    -   Create an NGINX service.

        ```
        kubectl run nginx --image=registry.aliyuncs.com/acs/netdia:latest
        ```

    -   Query the pod information.

        ```
        kubectl get pod
        ```

        ```
        NAME                                   READY     STATUS    RESTARTS   AGE
        nginx-2721357637-dvwq3                 1/1       Running   1          6s
        ```

2.  Set the type of NGINX service to `LoadBalancer`. This allows you to access the service by using an SLB instance over the Internet.

    -   Set the type of NGINX service to LoadBalancer.

        ```
        kubectl expose deployment nginx --port=80 --target-port=80 --type=LoadBalancer
        ```

    -   Query the service information.

        ```
        kubectl get svc
        ```

        ```
        NAME                  CLUSTER-IP      EXTERNAL-IP      PORT(S)                        AGE
        nginx                 172.19.XX.XX    101.37.XX.XX     80:31891/TCP                   4s
        ```

3.  Enter `http://101.37.XX.XX` in a browser to access the service.


**Method 2:**

1.  Write the following content to the `nginx-svc.yml` file.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        run: nignx
      name: nginx-01
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

2.  Run the following command to create an NGINX service of the LoadBalancer type:

    ```
    kubectl apply -f nginx-svc.yml
    ```

3.  Run the following command to obtain the public IP address of the service:

    ```
    kubectl get service
    ```

    ```
    NAME           TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE9d
    ngi-01nx       LoadBalancer   172.19.XX.XX    101.37.XX.XX     80:32325/TCP   3h
    ```

4.  Copy `http://101.37.XX.XX` into the address bar of your browser and press the Enter key to access the NGINX service.


## Use the Dashboard module in the ACK console

1.  Write the following content to the `nginx-svc.yml` file.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        run: nginx
      name: http-svc
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

2.  Log on to the [ACK console](https://cs.console.aliyun.com).

3.  In the left-side navigation pane of the ACK console, click **Clusters**.

4.  On the Clusters page, find the specific cluster, and choose **More** \> **Dashboard** in the Actions column of the cluster.

5.  Click **CREATE** to create a service.

    ![Create a service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3705129951/p9066.png)

6.  Click the **CREATE FROM FILE** tab. Select the nginx-svc.yml file.

7.  Click **UPLOAD**.

    After the file is uploaded, an NGINX service of the LoadBalancer type and an SLB instance is created. The service name is `http-svc`.

8.  In the left-side navigation pane, select the default namespace. On the Overview page, you can view services in the default namespace in the Services section.

    You can find the newly created `http-svc` service. The external endpoint is `114.55.XX.XX:80`.

9.  Enter http://114.55.XX.XX:80 in a browser to access the service.


## Use the ACK console

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the **Deployments** tab, click **Create from Template**.

6.  Select Custom in **Sample Template**, and copy the following content to the **Template** section:

    ```
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        run: nginx
      name: ngnix
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

7.  Click **CREATE**.

    In the left-side navigation pane, click **Services**, and select the specific namespace from the namespace drop-down list to view the services deployed in the namespace.


## References

Alibaba Cloud SLB supports a variety of features. For example, you can configure the health check, billing method, and instance type of an SLB instance by setting parameters. For more information, see [Use annotations to configure SLB instances](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure SLB instances.md).

