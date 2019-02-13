# Use Istio to deploy application services across Kubernetes and ECS instances {#concept_qzh_bb5_cfb .concept}

Starting from v0.2, Istio provides mesh expansion. With this feature, you can integrate non-Kubernetes services that typically run on VMs or bare metal hosts with the Istio service mesh that runs on your Kubernetes cluster.

Alibaba Cloud Container Service for Kubernetes supports the Istio mesh expansion capabilities. This topic uses an example from the Istio official website to details how to use Istio to deploy application services across Kubernetes and ECS instances.

## Mesh expansion {#section_gvz_cb5_cfb .section}

Mesh expansion is a method based on the Istio service mesh deployed on Kubernetes. With this method, you can integrate VMs or bare metal hosts into the service mesh.

Mesh expansion is suitable for when you need to migrate your applications from your local system to cloud services. In a microservices system, not all workloads can run in Kubernetes. This means you may encounter scenarios in which you can only operate and maintain some services in Kubernetes, while other services run on VMs or bare metal hosts.

With the Istio control plane, you can manage services across Kubernetes and VMs or bare metal hosts, and ensure that all your services can continue to run normally.

## Create a Kubernetes cluster and install Istio {#section_hvz_cb5_cfb .section}

Alibaba Cloud Container Service for Kubernetes 1.11.5 is now available. You can quickly create a Kubernetes cluster through the Container Service console. For more information, see [Create a Kubernetes cluster](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

**Note:** You must make sure that you can connect to your Kubernetes cluster by using kubectl. For more information, see [Connect to a Kubernetes cluster by using kubectl](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

Deploy Istio through the app catalog. Create the `istio-system` namespace through a command or the console.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, choose **Store** \> **App Catalog**, and click `ack-istio` on the right side.
3.  On the displayed page, select `istio-system` from the namespace drop-down list, and click **Values**. You can edit parameters to customize your Istio.

**Note:** The readme document on the page provides the installation and removal information, including common questions about Custom Resource Definition \(CRD\) versions.

## Install the sample application in your Kubernetes cluster {#section_jvz_cb5_cfb .section}

Run the following commands or use the console to create the `bookinfo` namespace, and then deploy the modified application. In the modified application, the `details` component is removed and `ingressgateway` is defined.

To obtain the files used in this example, see [Istio multi-cluster sample files](https://github.com/osswangxining/istio-meshexpansion?spm=a2c4e.11153940.blogcont622558.29.35307db13kaxoA).

```
kubectl create ns bookinfo
kubectl label namespace bookinfo istio-injection=enabled
kubectl apply -n bookinfo -f ./bookinfo/bookinfo-without-details.yaml
kubectl apply -n bookinfo -f ./bookinfo/bookinfo-gateway.yaml
```

Both the `details` and the database components of the application deployment run on the ECS instance that is outside the Kubernetes system.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/155007342612682_en-US.png)

Access the `/productpage` page through the address exposed by `ingressgateway` and verify that the `details` part cannot be displayed.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/155007342612683_en-US.png)

## Configure your Kubernetes {#section_lvz_cb5_cfb .section}

1.  If you have not set internal load balancers for Kube DNS, Pilot, Mixer, and Citadel when you install Istio, you need to run the following command:

    ```
    kubectl apply -f ./mesh-expansion.yaml
    ```

    As shown in the following figure, the four services are created.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/155007342612684_en-US.png)

2.  Generate the cluster.env Istio configuration file and the kubedns DNS configuration file both of which are to be deployed in the VMs. The cluster.env file contains the range of the cluster IP addresses that will be intercepted. The kubedns file contains the cluster service names that can be resolved by the applications on the VMs and then will be intercepted and forwarded by the sidecar.

    To generate the configuration files, run the following command:

    ```
    ./setupMeshEx.sh generateClusterEnvAndDnsmasq
    ```

    Configuration file cluster.env

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/155007342612685_en-US.png)

    Configuration file kubedns

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/155007342612686_en-US.png)


## Set the ECS instance {#section_nvz_cb5_cfb .section}

Configure your working environment to communicate with the ECS instance. Generate an SSH key and assign it to the ECS instance. You can run the `ssh root@<ECS_HOST_IP>` command to check if you can connect to the ECS instance.

To generate a public key, run the following command:

```
ssh-keygen -b 4096 -f ~/.ssh/id_rsa -N ""
```

**Note:** To ensure that the ECS instance and Kubernetes are mutually accessible over the Internet, you need to add them to the same security group.

With Alibaba Cloud Container Service for Kubernetes, you can quickly configure an ECS instance by running the following script:

```
export SERVICE_NAMESPACE=default
./setupMeshEx.sh machineSetup root@<ECS_HOST_IP>
```

Run the following command to check the running process:

```
ps aux |grep istio
```

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/155007342612687_en-US.png)

Run the following command to check if the node agent authenticated by Istio is running in a healthy status:

```
sudo systemctl status istio-auth-node-agent
```

## Run services on the ECS instance {#section_qvz_cb5_cfb .section}

As shown in the preceding deployment figure, two services run on the ECS instance: one is the Details service, the other one is the Database service.

## Run the Details service on the ECS instance {#section_rvz_cb5_cfb .section}

Run the following commands to simulate \(by using Docker only\) the `Details` service, run the service on the ECS instance, and expose port 9080 for the service.

```
docker pull istio/examples-bookinfo-details-v1:1.8.0
docker run -d -p 9080:9080 --name details-on-vm istio/examples-bookinfo-details-v1:1.8.0
```

Configure the sidecar to intercept the port. You need to configure this in the /var/lib/istio/envoy/sidecar.env path and use the ISTIO\_INBOUND\_PORTS environment variable.

Run the following command on the VM in which the service runs:

```
echo"ISTIO_INBOUND_PORTS=9080,8080" > /var/lib/istio/envoy/sidecar.env
systemctl restart istio
```

## Register the Details service with Istio {#section_svz_cb5_cfb .section}

Run the following command to view the IP address of the VM so that you can add it to the service mesh:

```
hostname -I
```

Manually configure a selector-less service and endpoints. The selector-less service is used to host services that are not backed by Kubernetes pods. For example, run the following command to register the `Details` service on a server that has the permissions to modify Kubernetes services and supports istioctl commands:

```
istioctl -n bookinfo register details 192.168.3.202 http:9080
```

Access the `/productpage` page again to verify that the `details` part is displayed as shown in following figure.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/155007342612688_en-US.png)

## Update the Ratings service to the version that can access a database {#section_tvz_cb5_cfb .section}

By default, the Ratings service cannot access any database. Run the following command to update the service version so that the service can access the database:

```
kubectl apply -f ./bookinfo/bookinfo-ratings-v2-mysql-vm.yaml
kubectl apply -f ./bookinfo/virtual-service-ratings-mysql-vm.yaml
```

Access the `/productpage` page to verify that the `Ratings` part cannot be displayed as shown in the following figure. Then, you need to build a database service on the ECS instance and add the service to Istio.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/155007342712689_en-US.png)

## Run a database service on the ECS instance {#section_vvz_cb5_cfb .section}

On the VM, run MariaDB as the backend for the Ratings service, and set MariaDB to be remotely accessible.

```
apt-get update && apt-getinstall -y mariadb-server
sed -i 's/127\. 0\. 0\. 1/0\. 0\. 0\. 0/g' /etc/mysql/mariadb.conf.d/50-server.cnf
sudo mysql
# Grant the root permission.
GRANT ALL PRIVILEGESON *. * TO'root'@'localhost'IDENTIFIEDBY'password'WITHGRANTOPTION;
quit;
sudo systemctl restart mysql
```

Run the following command to initialize the Ratings database on the VM:

```
curl -q https://raw.githubusercontent.com/istio/istio/master/samples/bookinfo/src/mysql/mysqldb-init.sql | mysql -u root -ppassword
```

To view different outputs of the Bookinfo application, run the following command to modify the rating records to generate different rating data that are displayed on the page:

```
mysql -u root -ppassword test -e "select * from ratings;"
mysql -u root -ppassword test -e  "update ratings set rating=2;select * from ratings;"
```

## Register the database service into Istio {#section_wvz_cb5_cfb .section}

Configure the sidecar to intercept the port. You need to configure this in the /var/lib/istio/envoy/sidecar.env path and use the ISTIO\_INBOUND\_PORTS environment variable.

Run the following command on the VM in which the service runs:

```
echo"ISTIO_INBOUND_PORTS=3306,9080,8080" > /var/lib/istio/envoy/sidecar.env
systemctl restart istio
```

Run the following command to register the database service on a server that has the permissions to modify Kubernetes services and supports istioctl commands:

```
istioctl-nbookinforegistermysqldb 192.168.3.202 3306
```

Now Kubernetes pods and other servers included by mesh expansion can access the database service running on this server.

Access the `/productpage` page to verify that both the Details and Ratings parts can be displayed and these two services are provided by the ECS instance.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21312/155007342712690_en-US.png)

## Conclusion {#section_zvz_cb5_cfb .section}

Alibaba Cloud Container Service for Kubernetes provides the Istio mesh expansion capabilities. This topic uses a sample application from the Istio official website to details how to use Istio to deploy application services across Kubernetes and ECS instances.

We recommend that you use Alibaba Cloud Container Service for Kubernetes to quickly build Istio, an open management platform for microservices, and integrate Istio with the microservice development of your project.

