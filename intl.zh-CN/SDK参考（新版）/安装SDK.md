# 安装SDK

本文介绍安装容器服务SDK。

## 步骤一：安装Tea-console

调试SDK示例前需要安装Tea-console，安装方式如下：

-   Java SDK

通过Maven进行安装，请在项目文件中加入以下依赖：

```
	<dependency>
  		<groupId>com.aliyun</groupId>
  		<artifactId>tea-console</artifactId>
  		<version>0.0.1</version>
	</dependency>
```

-   Python SDK

推荐使用PyPI进行安装，执行以下命令：

```
pip install alibabacloud_tea_console
```

-   GO SDK

推荐使用go工具进行安装，执行以下命令：

```
go get github.com/alibabacloud-go/tea-console/client:v1.0.0
```

-   TypeScript/Node.js SDK

通过NPM工具进行安装，执行以下命令：

```
npm install @alicloud/tea-console@^1.0.0 --save
```

## 步骤二：安装容器服务SDK

以下命令默认安装的是最新版本的SDK，您可以根据开发需要到各语言的对应仓库中寻找适用版本。

-   Java SDK

在Maven项目文件中添加以下依赖：

```
  <dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>cs20151215</artifactId>
    <version>[2.4.1,)</version>
  </dependency>
```

-   Go SDK

推荐使用go工具进行安装：

```
go get github.com/alibabacloud-go/cs-20151215/v2/client
```

-   .NET SDK

推荐使用dotnet工具进行安装：

```
dotnet add package AlibabaCloud.SDK.CS20151215
```

-   PHP SDK

推荐使用composer工具进行安装：

```
composer require alibabacloud/cs-20151215
```

-   TypeScript/Node.js SDK

推荐使用NPM工具进行安装：

```
npm install @alicloud/cs20151215 --save
```

-   Python SDK

推荐使用PyPI工具进行安装：

```
pip install alibabacloud_cs20151215
```

