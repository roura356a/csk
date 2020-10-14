# Use Istio to deploy application services to a Kubernetes cluster and an ECS instance

Container Service for Kubernetes \(ACK\) provides the Istio mesh expansion capabilities. This topic uses a sample application from the Istio official website to describe how to use Istio to deploy application services to a Kubernetes cluster and an ECS instance.

Istio 0.2 and later support mesh expansion. This feature integrates non-Kubernetes services running on VMs or bare metal servers into an Istio service mesh that is deployed on a Kubernetes cluster.

## Mesh expansion

Mesh expansion allows you to integrate services running on VMs or bare metal servers into an Istio service mesh that is deployed on your Kubernetes cluster.

This solution applies to scenarios where you migrate your application services from your on-premises system to the cloud. Some microservices of an application may run outside a cluster. As a result, you can manage only part of application services in Kubernetes, but cannot manage services running on VMs or bare metal servers.

After you deploy application services to Kubernetes clusters and VMs or bare metal servers, you can manage the services through the Istio control plane. The services run smoothly when you manage them in a unified manner.

## Create a cluster and deploy Istio to the cluster

ACK 1.10.4 is available. You can create a cluster in the ACK console within a few clicks. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).

**Note:** Make sure that you can connect to the cluster through kubectl. For more information, see [Connect to Kubernetes clusters through kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to a cluster.md).

For information about how to deploy Istio through the App Catalog module in the ACK console, see the preceding topics about Istio. After you deploy Istio, create namespace `istio-system` by using the kubectl command-line tool or in the ACK console.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Service Mesh** \> **Istio Management**.

3.  On the Deploy Istio page, select the cluster where you want to deploy Istio, and configure parameters based on your needs.

    **Note:**


## Deploy a sample application to a cluster

Run the following commands or log on to the ACK console to create namespace `bookinfo` and deploy an updated application to a cluster. In this example, the updated application has the `details` service removed and the Istio ingress gateway is configured.

You can obtain the files used in this example from [Istio sample files](https://github.com/osswangxining/istio-meshexpansion?spm=a2c4e.11153940.blogcont622558.29.35307db13kaxoA).

```
kubectl create ns bookinfo
kubectl label namespace bookinfo istio-injection=enabled
kubectl apply -n bookinfo -f ./bookinfo/bookinfo-without-details.yaml
kubectl apply -n bookinfo -f ./bookinfo/bookinfo-gateway.yaml
```

In this example, an application based on the sample application from the Istio official website is deployed. The `details` and database services of the application are running on an ECS instance that is outside the Kubernetes cluster.

![Deploy an application](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9655359951/p12682.png)

After you deploy the application, obtain the application IP address from the configurations of the Istio `ingressgateway`. Then, access the web page of the application. The page fails to display the `details` section, as shown in the following figure.

![error](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0755359951/p12683.png)

## Configure a Kubernetes cluster

1.  Assume that you have not configured internal load balancers for the Pilot, Mixer, Citadel, and Kubernetes DNS services when you deploy Istio. To enable load balancing for these services, run the following command:

    ```
    kubectl apply -f ./mesh-expansion.yaml
    ```

    The following command output appears:

    ![Services](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0755359951/p12684.png)

2.  Generate two files to be deployed to a virtual machine, including the Istio configuration file named cluster.env and the DNS configuration file named kubedns. The cluster.env file contains the range of IP addresses to be blocked from accessing services in the Isito mesh. The kubedns file contains the service names that can be resolved by the application services on the VM. Then, the sidecar proxies intercept and forward the service requests.

    To generate the configuration files, run the following command:

    ```
    ./setupMeshEx.sh generateClusterEnvAndDnsmasq
    ```

    Sample configuration file cluster.env:

    ![nodes](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0755359951/p12685.png)

    Sample configuration file kubedns:

    ![nodes](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0755359951/p12686.png)


## Configure an ECS instance

Connect to an ECS instance. Generate an SSH key pair and bind it to the ECS instance. To check whether you can connect to an ECS instance, run the `ssh root@<ECS_HOST_IP>` command.

To generate a public key, run the following command:

```
ssh-keygen -b 4096 -f ~/.ssh/id_rsa -N ""
```

**Note:** To ensure that the cluster can connect to the ECS instance over the Internet, add the cluster and the ECS instance to the same security group.

ACK allows you to configure an ECS instance in a simplified manner. You can run the following commands to configure an ECS instance:

```
export SERVICE_NAMESPACE=default
./setupMeshEx.sh machineSetup root@<ECS_HOST_IP>
```

Run the following command to query the running processes:

```
ps aux |grep istio
```

![nodes](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0755359951/p12687.png)

Run the following command to query the health status of the node agent used in Istio authentication:

```
sudo systemctl status istio-auth-node-agent
```

## Deploy services to the ECS instance

In the preceding example, the details and database services must be running outside the cluster. The following content describes how to deploy the two services to the ECS instance.

## Deploy the details service to the ECS instance

Run the following commands to deploy a Docker-based `details` service to the ECS instance. The details service can be accessed through port 9080.

```
docker pull istio/examples-bookinfo-details-v1:1.8.0
docker run -d -p 9080:9080 --name details-on-vm istio/examples-bookinfo-details-v1:1.8.0
```

Install a sidecar proxy to intercept traffic destined for port 9080. You can obtain the configuration file from the /var/lib/istio/envoy/sidecar.env path. The environment variable is ISTIO\_INBOUND\_PORTS.

To configure a sidecar, run the following commands on the ECS instance:

```
echo"ISTIO_INBOUND_PORTS=9080,8080" > /var/lib/istio/envoy/sidecar.env
systemctl restart istio
```

## Deploy a database service to the ECS instance

Install a sidecar proxy to intercept traffic destined for port 9080. You can obtain the configuration file from the /var/lib/istio/envoy/sidecar.env path. The environment variable is ISTIO\_INBOUND\_PORTS.

To configure a sidecar, run the following commands on the ECS instance:

```
echo"ISTIO_INBOUND_PORTS=3306,9080,8080" > /var/lib/istio/envoy/sidecar.env
systemctl restart istio
```

Run the following command to deploy a database service to the ECS instance that can access Kubernetes resources and supports istioctl commands:

```
istioctl-nbookinforegistermysqldb 192.168.3.202 3306
```

In this case, pods running on nodes in the Kubernetes cluster and servers that run services in the Istio mesh can access the database service running on the ECS instance.

Then, access the web page of the application. The details and ratings sections are available. The two services are running on the ECS instance.

![error](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0755359951/p12690.png)

## Conclusion

ACK enables the Istio mesh expansion capabilities. This topic uses a sample application from the Istio official website to describe how to use Istio to deploy application services to a Kubernetes cluster and an ECS instance. In this case, you can install Istio for your cluster and deploy microservices of applications.

