# Install ACK SDK

This topic describes how to install Container Service for Kubernetes \(ACK\) SDK.

## Step 1: Install Tea-console

Before you debug the SDK, you must install Tea-console:

-   Java SDK

To install Tea-console for Java by using Apache Maven, add the following dependencies to the project files:

```
	<dependency>
  		<groupId>com.aliyun</groupId>
  		<artifactId>tea-console</artifactId>
  		<version>0.0.1</version>
	</dependency>
```

-   Python SDK

To install Tea-console for Python by using Python Package Index \(PyPI\), run the following command:

```
pip install alibabacloud_tea_console
```

-   GO SDK

To install Tea-console for Go, run the following command:

```
go get github.com/alibabacloud-go/tea-console/client:v1.0.0
```

-   TypeScript/Node.js SDK

To install Tea-console for TypeScript in Node.js by using node package manager \(NPM\), run the following command:

```
npm install @alicloud/tea-console@^1.0.0 --save
```

## Step 2: Install ACK SDK

You can run the following commands to install the latest ACK SDK. Select an SDK version as needed from the code repositories for different languages.

-   Java SDK

To install ACK SDK for Java, add the following dependency to the Maven project file:

```
  <dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>cs20151215</artifactId>
    <version>[2.4.1,)</version>
  </dependency>
```

-   Go SDK

To install ACK SDK for Go, we recommend that you run the following command:

```
go get github.com/alibabacloud-go/cs-20151215/v2/client
```

-   .NET SDK

To install ACK SDK for .NET by using dotnet, we recommend that you run the following command:

```
dotnet add package AlibabaCloud.SDK.CS20151215
```

-   PHP SDK

To install ACK SDK for PHP by using composer, we recommend that you run the following command:

```
composer require alibabacloud/cs-20151215
```

-   TypeScript/Node.js SDK

To install ACK SDK for TypeScript in Node.js by using node package manager \(NPM\), we recommend that you run the following command:

```
npm install @alicloud/cs20151215 --save
```

-   Python SDK

To install ACK SDK for Python by using Python Package Index \(PyPI\), we recommend that you run the following command:

```
pip install alibabacloud-cs20151215
```

