Connect to an ApsaraDB RDS instance from an ASK cluster 
============================================================================

This topic describes how to connect to an ApsaraDB RDS instance from an elastic container instance. 

ApsaraDB RDS provides five types of database engines. You can create ApsaraDB RDS instances based on your business requirements. For more information, see [Quick Start](https://help.aliyun.com/document_detail/26174.html?spm=a2c4g.11186623.6.585.13b933238aKH0l). 

In the following examples, ApsaraDB RDS for MySQL is used.

Prerequisites 
----------------------------------

* An ApsaraDB RDS instance is created, and a database account is created on the instance. For more information, see [General workflow to use RDS for MySQL](https://help.aliyun.com/document_detail/26124.html?spm=a2c4g.11186623.6.621.1c0f60ff1Q46IO).

  




<!-- -->

* A serverless Kubernetes (ASK) cluster is created, and Virtual Kubelet is deployed in the cluster. For more information about how to create an ASK cluster, see [Create an ASK cluster](https://help.aliyun.com/document_detail/86377.html#task-e3c-311-ydb).

  





**Notice**

We recommend that you deploy the ApsaraDB RDS instance and ASK cluster in the same virtual private cloud (VPC). If they are deployed in different VPCs, you can connect them over the Internet. In the following example, the ApsaraDB RDS instance and ASK cluster are deployed in the same VPC.

Configure the whitelist 
--------------------------------------------

**Note**



ApsaraDB for RDS controls network access through IP address whitelists and security groups.

* IP address whitelists

  




You can add IP addresses to the IP address whitelist of an ApsaraDB RDS instance to allow only specified IP addresses to access the ApsaraDB RDS instance. The default whitelist contains only the IP address 127.0.0.1. This indicates that no hosts are allowed to access the ApsaraDB RDS instance. 

* Security groups

  




A security group is a virtual firewall that is used to control the inbound and outbound traffic of the elastic container instance in the security group. After you add a security group to the whitelist of an ApsaraDB RDS instance, all elastic container instances in the security group can access the ApsaraDB RDS instance.



* Connect to the internal endpoint of the ApsaraDB RDS instance

  




1. Log on to the [ApsaraDB RDS console](https://rdsnext.console.aliyun.com/?accounttraceid=dfbef66e3a954ab098b4f9498e785c19fsek#/detail/rm-bp1tlj66mf2lp5826/link/instanceLink?region=cn-hangzhou), click the ApsaraDB RDS instance, and then navigate to the **Database Connection** page. The internal endpoint of the instance is displayed on this page. 

![host](../images/p132323.png)

The internal endpoint belongs to the host of the ApsaraDB RDS instance. 

2. Configure the IP address whitelist

Add the CIDR block of the VPC or vSwitch with which the pod is associated to the IP address whitelist. Alternatively, you can add instances to the security group. This allows these instances to access the ApsaraDB RDS instance. 

![The security group and vSwitch](../images/p132325.png)

* Connect to the public endpoint of the ApsaraDB RDS instance

  




1. Apply for a public endpoint

If you want to access an ApsaraDB RDS instance over the Internet, you must connect to the public endpoint of the ApsaraDB RDS instance. By default, an ApsaraDB RDS instance does not have a public endpoint. To access an ApsaraDB RDS instance over the Internet, you must log on to the ApsaraDB RDS console, navigate to the Database Connection page, and then apply for a public endpoint for the instance. 

![1](../images/p131788.png)
**Note**

* When you connect to the public endpoint of an ApsaraDB RDS instance, the security of the instance is compromised. Proceed with caution.

  

* To accelerate and secure data transmission, we recommend that you migrate your applications to an elastic container instance that is deployed in the same region and has the same network type as the ApsaraDB RDS instance. This allows you to connect to the internal endpoint of the ApsaraDB RDS instance.

  




<!-- -->

* For more information about how to connect to the public endpoint of an ApsaraDB RDS instance, see [How do I connect to an ApsaraDB RDS instance?](https://help.aliyun.com/knowledge_detail/41843.html#concept-41843-zh)

  




After the ApsaraDB RDS instance obtains a public endpoint, you can access the ApsaraDB RDS instance by connecting to the public endpoint. 

2. Configure the IP address whitelist

* Associate the vSwitch of the elastic container instance with a NAT gateway. Then, add the public IP address of the NAT gateway to the IP address whitelist of the ApsaraDB RDS instance.

  

* Add the elastic IP address (EIP) of the elastic container instance to the IP address whitelist of the ApsaraDB RDS instance.

  



**Note**

To connect to the ApsaraDB RDS instance from an elastic container instance, you must [enable Internet access](https://help.aliyun.com/document_detail/99146.html?spm=a2c4g.11174283.6.584.7f9f4b5brwjsvb) for the elastic container instance. 

* Method 1: Associate the VPC with a NAT gateway and attach an EIP to the NAT gateway. Then, add the public IP address of the vSwitch of the elastic container instance to the IP address whitelist of the ApsaraDB RDS instance. We recommend that you use this method.

  

* Method 2: Associate the elastic container instance with an EIP and add the EIP to the IP address whitelist of the ApsaraDB RDS instance.

  





**Notice**

The whitelist provides enhanced access control for the ApsaraDB RDS instance. We recommend that you check the whitelist on a regular basis. The whitelist does not affect the performance or services of the ApsaraDB RDS instance. For more information, see [Configure an IP address whitelist for an ApsaraDB RDS for MySQL instance](https://help.aliyun.com/document_detail/43185.html?spm=5176.13643035.0.0.1c2b14500OaU7l).



Connect to the ApsaraDB RDS instance from a Kubernetes cluster 
-----------------------------------------------------------------------------------

The following example shows you how to connect to the internal endpoint of the ApsaraDB RDS instance:

Add the CIDR block of the vSwitch of the pod to the IP address whitelist of the ApsaraDB RDS instance. 

![q](../images/p132335.png)![a](../images/p132336.png)



1. **Create a ConfigMap** 

A ConfigMap decouples the environment variables and container image settings. It allows you to modify application settings. Log on to the [Container Service for Kubernetes (ACK) console](https://cs.console.aliyun.com/?spm=5176.12818093.0.dcsk.77c516d0ufbVnC&accounttraceid=db0633bd94804c87b88561eaec1fd8d9eqcw#/k8s/configmap/list), click the ACK cluster that you want to manage, and then choose **Configurations \> ConfigMaps** in the left-side navigation pane to create a ConfigMap. 

![1](../images/p132273.png)

Add the information that is required for connecting to the ApsaraDB RDS instance to the ConfigMap. 

![2](../images/p132280.png)
**Notice**

In the host field, enter the internal endpoint of the ApsaraDB RDS instance. 

![2](../images/p132337.png)

2. **Create a Secret** 

If you want to store confidential data such as the database name, username, and password, create a Secret to pass the data as environment variables to the applications in a secured way. 

![secret](../images/p132281.png)

Add the username and password of the account created on the ApsaraDB RDS instance. 

![secret1](../images/p132282.png)

3. Create a pod to connect to the ApsaraDB RDS instance

Log on to the [ACK console](https://cs.console.aliyun.com/?spm=5176.12818093.0.dcsk.77c516d0ufbVnC&accounttraceid=db0633bd94804c87b88561eaec1fd8d9eqcw#/k8s/deployment/list), click the ACK cluster that you want to manage, and then choose **Workloads \> Deployments** in the left-side navigation pane. Then, create a pod by using a template. 
**Note**

ApsaraDB RDS for MySQL is fully compatible with open source MySQL. You can connect to an ApsaraDB RDS instance from a database client in a similar way that you use to connect to an open source MySQL database. The following Python code block shows an example on how to connect to the ApsaraDB RDS instance:

    apiVersion: v1
    kind: Pod
    metadata:
      labels:
        name: rds-test
      name: rds-test
    spec:
      containers:
      - name: test-rds
        image: registry.cn-hangzhou.aliyuncs.com/eci_open/sqlclient:latest # A Python job that is used to connect to the image of the ApsaraDB RDS instance.
    imagePullPolicy: IfNotPresent
        command: [ "/bin/bash", "-c", "python3 /testapp/mysqlclient.py"]
        env:
        - name: MYSQL_HOST # This key is different from that in the ConfigMap.
          valueFrom:
            configMapKeyRef:
              name: sqlconfig  # This key is provided by the ConfigMap.
              key: host  # Enter the value of the key.
        - name: MYSQL_PORT
          valueFrom:
            configMapKeyRef:
              name: sqlconfig
              key: port
        - name: MYSQL_DB
          valueFrom:
            configMapKeyRef:
              name: sqlconfig
              key: database
        - name: MYSQL_USERNAME
          valueFrom:
              secretKeyRef:
                name: sqlsecret
                key: username
        - name: MYSQL_PWD
          valueFrom:
            secretKeyRef:
              name: sqlsecret
              key: password
      restartPolicy: Never





In this example, a Python script is used to connect to the ApsaraDB RDS instance and insert two data entries into the ApsaraDB RDS instance. Sample Python script:

    import pymysql
    import os
    
    config = {
        'host': str(os.getenv('MYSQL_HOST')),
        'port': int(os.getenv('MYSQL_PORT')),
        'user': str(os.getenv('MYSQL_USERNAME')),
        'password': str(os.getenv('MYSQL_PWD')),
        'database': str(os.getenv('MYSQL_DB')),
    }
    
    
    def mysqlClient():
        db = pymysql.connect(**config)
        try:
            cursor = db.cursor()
            cursor.execute("INSERT INTO username(user) VALUES('Mrs')")
            cursor.execute("INSERT INTO username(user) VALUES('xiao')")
            cursor.execute("SELECT*FROM username")
            result = cursor.fetchall()
        except Exception as e:
            print('System Error: ',e)
        finally:
            db.commit()
            cursor.close()
            db.close()
        return result
    
    
    if__name__ == '__main__':
        mysqlClient()




**Note**

You can connect to the internal or public endpoint of an ApsaraDB RDS instance. The preceding example shows how to connect to the internal endpoint of an ApsaraDB RDS instance. We recommend that you connect to the internal endpoint of an ApsaraDB RDS instance for accelerated and secure data transmission.

The following figure shows the result after you are connected to the ApsaraDB RDS instance.

![dms-db](../images/p132492.png)



FAQ 
------------------------

**Notice**

The solutions to connection errors vary based on the actual environment. 

1. What can I do if I fail to connect to the internal endpoint of an ApsaraDB RDS instance from a pod?

2. What can I do if I fail to connect to the public endpoint of an ApsaraDB RDS instance?

For more information, see [What can I do if I fail to connect to an ApsaraDB RDS instance?](https://help.aliyun.com/knowledge_detail/96028.html)



Common causes of connection failures

* [Different network types](https://help.aliyun.com/knowledge_detail/96028.html#title-gty-1nq-r8g)

  

* [Different VPCs](https://help.aliyun.com/knowledge_detail/96028.html#title-hvd-0r0-yiv)

  

* [DNS resolution failures or errors](https://help.aliyun.com/knowledge_detail/96028.html#title-6oq-qzd-4fa)

  

* [Different regions](https://help.aliyun.com/knowledge_detail/96028.html#title-0t4-i4f-1iv)

  

* [IP address whitelist settings](https://help.aliyun.com/knowledge_detail/96028.html#title-xgw-uw3-8jm)

  

* [A read-only instance does not have a whitelist](https://help.aliyun.com/knowledge_detail/96028.html#title-8xc-gt0-2l2)

  

* [Invalid internal or public endpoint](https://help.aliyun.com/knowledge_detail/96028.html#title-k7f-geh-qri)

  

* [Exhausted connection quota](https://help.aliyun.com/knowledge_detail/96028.html#title-owv-w9q-xa3)

  

* [Invalid username or password](https://help.aliyun.com/knowledge_detail/96028.html#JeQbH)

  

* [Invalid host address](https://help.aliyun.com/knowledge_detail/96028.html#CbSPT)

  










