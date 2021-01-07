# Java

本文介绍如何安装Alibaba Cloud ACK SDK for Java和如何快速使用Alibaba Cloud ACK SDK for Java完成常见操作，如创建集群、集群扩容以及添加已有ECS实例至Kubernetes集群。

在安装和使用Alibaba Cloud ACK SDK for Java前，请确保您已经完成以下操作。

-   安装Java环境。

    Alibaba Cloud ACK SDK for Java要求使用JDK1.6或更高版本。

-   注册阿里云账号并生成访问密钥（AccessKey）。

    详情参见[获取AccessKey]()。


## 源码地址

请参见以下信息获取源码。

|编程语言|Github地址|Github文档|
|----|--------|--------|
|Java|[aliyun-openapi-java-sdk](https://github.com/aliyun/aliyun-openapi-java-sdk/tree/master/aliyun-java-sdk-cs)|[README.md](https://github.com/aliyun/aliyun-openapi-java-sdk/blob/master/README.md)|

## 安装Alibaba Cloud ACK SDK for Java

-   方法一：添加Maven依赖 （推荐）

    如果您使用Maven管理Java项目，可以通过在pom.xml文件中添加Maven依赖安装Alibaba Cloud ACK SDK for Java。查看各云产品的Maven依赖信息，请参见[Alibaba Cloud SDK for Java](https://open.aliyun.com/sdk)。

    以使用Alibaba Cloud ACK SDK for Java为例，您只需添加以下两个依赖来安装ACK SDK核心库和Alibaba Cloud ACK SDK for Java。

    ```
    <dependency>
        <groupId>com.aliyun</groupId>
        <artifactId>aliyun-java-sdk-core</artifactId>
        <version>4.5.0</version>
    </dependency>
    <dependency>
        <groupId>com.aliyun</groupId>
        <artifactId>aliyun-java-sdk-cs</artifactId>
        <version>4.7.15</version>
    </dependency>
    ```

    如果Maven没有从中央存储库下载.jar包，则需要将此依赖项添加到pom.xml文件中，否则将报告`NoClassDefFoundError`异常。

    ```
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.8.5</version>
    </dependency>
    ```

-   方法二：在集成开发环境中导入JAR文件

    无论您使用Eclipse还是IntelliJ作为集成开发环境，都可以通过导入JAR文件的方式安装Alibaba Cloud ACK SDK for Java。下载各云产品的JAR文件，请参见[Alibaba Cloud SDK for Java](https://open.aliyun.com/sdk)。

    -   使用Eclipse

        在Eclipse的项目中安装Alibaba Cloud ACK SDK for Java，操作如下：

        1.  将下载的aliyun-java-sdk-xxx.jar文件复制到您的项目文件夹中。
        2.  在Eclipse中打开您的项目，右键单击该项目，单击**Properties**。
        3.  在弹出的对话框中，单击**Java Build Path** \> **Libraries** \> **Add JARs**添加下载的JAR文件。
        4.  单击**Apply and Close**。
    -   使用IntelliJ

        在IntelliJ的项目中安装Alibaba Cloud SDK for Java，操作如下：

        1.  将下载的aliyun-java-sdk-xxx.jar文件复制到您的项目文件夹中。
        2.  在IntelliJ中打开您的项目，在菜单栏中单击**File** \> **Project Structure**。
        3.  单击**Apply**，然后单击**OK**。

## 示例

-   创建集群

    以下示例指定Master节点数为3、Worker节点数为3，用于创建一个的新的Kubernetes集群实例。有关更多参数详情，请参见[创建Kubernetes专有版集群](/cn.zh-CN/API参考/集群/创建集群/创建Kubernetes专有版集群.md)。

    ```
    package com.aliyun.demo.cs;
    
    import com.aliyuncs.profile.DefaultProfile;
    import com.alibaba.fastjson.JSONObject;
    import com.aliyuncs.DefaultAcsClient;
    import com.aliyuncs.IAcsClient;
    import com.aliyuncs.exceptions.ClientException;
    import com.aliyuncs.exceptions.ServerException;
    import com.aliyuncs.http.FormatType;
    import com.aliyuncs.cs.model.v20151215.*;
    
    import java.util.Arrays;
    import java.util.HashMap;
    import java.util.Map;
    
    public class Main {
        public static void main(String[] args) throws Exception {
            // 创建DefaultAcsClient实例并初始化。
            DefaultProfile profile = DefaultProfile.getProfile(
                    "<your-region-id>",          // 地域ID
                    "<your-access-key-id>",      // RAM账号的AccessKey ID
                    "<your-access-key-secret>"); // RAM账号AccessKey Secret
            IAcsClient client = new DefaultAcsClient(profile);
            // 创建API请求并设置参数。
            CreateClusterRequest request = new CreateClusterRequest();
            final Map<String, Object> diskParams = new HashMap<String, Object>() {{
                put("category", "cloud_ssd");
                put("size", 500);
            }};
            Map<String, Object> createParams = new HashMap<String, Object>() {{
                put("cluster_type", "Kubernetes");
                put("name", "my-test-Kubernetes-cluster");
                put("region_id", "cn-beijing");
                put("kubernetes_version", "1.12.6-aliyun.1");
                put("master_count", "3");
                put("master_vswitch_ids", Arrays.asList("vsw-2ze48rkq464rsdts****", "vsw-2ze48rkq464rsdts1****", "vsw-2ze48rkq464rsdts1****"));
                put("master_instance_types", Arrays.asList("ecs.sn1.medium", "ecs.sn1.medium", "ecs.sn1.medium"));
                put("master_system_disk_category", "cloud_efficiency");
                put("master_system_disk_size", "40");
                put("worker_instance_types", Arrays.asList("ecs.sn2.3xlarge"));
                put("worker_system_disk_category", "cloud_efficiency");
                put("worker_system_disk_size", "120");
                put("worker_vswitch_ids", Arrays.asList("vsw-2ze48rkq464rsdts****"));
                put("worker_data_disks", Arrays.asList(diskParams));
                put("master_data_disks", Arrays.asList(diskParams));
                put("num_of_nodes", "3");
                put("container_cidr", "172.**.*.*/16");
                put("service_cidr", "172.**.*.*/20");
            }};
    
            if (createParams != null && !createParams.isEmpty()) {
                byte[] data = JSONObject.toJSONString(createParams).getBytes();
                request.setHttpContent(data, "UTF-8", FormatType.JSON);
            }
    
            // 发起请求并处理应答或异常。
            CreateClusterResponse response;
            try {
                response = client.getAcsResponse(request);
            } catch (ServerException e) {
                e.printStackTrace();
            } catch (ClientException e) {
                e.printStackTrace();
            }
        }
    }
    ```

-   集群扩容

    以下示例将在集群<your-cluster-id\>中扩容一台类型为ecs.c5.xlarge的节点。有关更多参数详情，请参见[扩容集群](/cn.zh-CN/API参考/集群/扩容集群/扩容集群.md)。

    ```
    package com.aliyun.demo.cs;
    
    import com.aliyuncs.profile.DefaultProfile;
    import com.alibaba.fastjson.JSONObject;
    import com.aliyuncs.DefaultAcsClient;
    import com.aliyuncs.IAcsClient;
    import com.aliyuncs.exceptions.ClientException;
    import com.aliyuncs.exceptions.ServerException;
    import com.aliyuncs.http.FormatType;
    import com.aliyuncs.cs.model.v20151215.*;
    
    import java.util.Arrays;
    import java.util.HashMap;
    import java.util.Map;
    
    public class Main {
        public static void main(String[] args) {
            // 创建DefaultAcsClient实例并初始化。
            DefaultProfile profile = DefaultProfile.getProfile(
                    "<your-region-id>",          // 地域ID
                    "<your-access-key-id>",      // RAM账号的AccessKey ID
                    "<your-access-key-secret>"); // RAM账号AccessKey Secret
            IAcsClient client = new DefaultAcsClient(profile);
            // 创建API请求并设置参数。
            ScaleClusterRequest request = new ScaleClusterRequest();
            request.setClusterId("<your-cluster-id>");
            Map<String, Object> scaleParams = new HashMap<String, Object>(){{
                put("key_pair","common");
                put("count","1");
                put("worker_instance_types",Arrays.asList("ecs.c5.xlarge"));
                put("worker_system_disk_category","cloud_efficiency");
                put("worker_data_disk","120");
            }};
    
            if (scaleParams != null && !scaleParams.isEmpty()) {
                byte[] data = JSONObject.toJSONString(scaleParams).getBytes();
                request.setHttpContent(data, "UTF-8", FormatType.JSON);
            }
    
            // 发起请求并处理应答或异常。
            ScaleClusterResponse response;
            try {
                response = client.getAcsResponse(request);
            } catch (ServerException e) {
                e.printStackTrace();
            } catch (ClientException e) {
                e.printStackTrace();
            }
        }
    }
    ```

-   添加已有ECS实例至Kubernetes集群

    以下示例将实例ID为i-xxxx和i-yyyy的ECS添加到集群<your-cluster-id\>中。有关更多参数详情，请参见[添加已有实例到集群](/cn.zh-CN/API参考/节点/添加已有实例到集群.md)。

    ```
    package com.aliyun.demo.cs;
    
    import com.aliyuncs.profile.DefaultProfile;
    import com.alibaba.fastjson.JSONObject;
    import com.aliyuncs.DefaultAcsClient;
    import com.aliyuncs.IAcsClient;
    import com.aliyuncs.exceptions.ClientException;
    import com.aliyuncs.exceptions.ServerException;
    import com.aliyuncs.http.FormatType;
    import com.aliyuncs.cs.model.v20151215.*;
    
    import java.util.Arrays;
    import java.util.HashMap;
    import java.util.Map;
    
    public class Main {
        public static void main(String[] args) {
            // 创建DefaultAcsClient实例并初始化。
            DefaultProfile profile = DefaultProfile.getProfile(
                    "<your-region-id>",          // 地域ID
                    "<your-access-key-id>",      // RAM账号的AccessKey ID
                    "<your-access-key-secret>"); // RAM账号AccessKey Secret
            IAcsClient client = new DefaultAcsClient(profile);
            // 创建API请求并设置参数。
            AttachInstancesRequest request = new AttachInstancesRequest();
            request.setClusterId("<your-cluster-id>");
            Map<String, Object> attachBody = new HashMap<String, Object>(){{
                put("password","Hello1234");
                put("instances",Arrays.asList("i-xxxx","i-yyyy"));
            }};
            if (attachBody != null && !attachBody.isEmpty()) {
                byte[] data = JSONObject.toJSONString(attachBody).getBytes();
                request.setHttpContent(data, "UTF-8", FormatType.JSON);
            }
            request.setHttpContentType(FormatType.JSON);
            // 发起请求并处理应答或异常。
            AttachInstancesResponse response;
            try {
                response = client.getAcsResponse(request);
            } catch (ServerException e) {
                e.printStackTrace();
            } catch (ClientException e) {
                e.printStackTrace();
            }
        }
    }
    ```

-   查询集群实例

    以下示例将查看指定集群<your-cluster-id\>的详细信息。有关更多参数详情，请参见[查询集群实例](/cn.zh-CN/API参考/集群/查询集群实例.md)。

    ```
    package com.aliyun.demo.cs;
    
    import com.aliyuncs.DefaultAcsClient;
    import com.aliyuncs.IAcsClient;
    import com.aliyuncs.cs.model.v20151215.*;
    import com.aliyuncs.exceptions.ClientException;
    import com.aliyuncs.exceptions.ServerException;
    
    import com.aliyuncs.profile.DefaultProfile;
    import com.google.gson.Gson;
    
    public class Main {
        public static void main(String[] args) {
            // 创建DefaultAcsClient实例并初始化。
            DefaultProfile profile = DefaultProfile.getProfile(
                    "<your-region-id>",          // 地域ID
                    "<your-access-key-id>",      // RAM账号的AccessKey ID
                    "<your-access-key-secret>"); // RAM账号AccessKey Secret
            IAcsClient client = new DefaultAcsClient(profile);
            // 创建API请求并设置参数。
            DescribeClusterDetailRequest request = new DescribeClusterDetailRequest();
            request.setClusterId("<your-cluster-id>");
    
            // 发起请求并处理应答或异常。
            DescribeClusterDetailResponse response;
            try {
                response = client.getAcsResponse(request);
                System.out.println(new Gson().toJson(response));
            } catch (ServerException e) {
                e.printStackTrace();
            } catch (ClientException e) {
                e.printStackTrace();
            }
        }
    }
    ```


