# 安装SDK

参考本文档安装容器服务SDK。

以下命令默认安装的是最新版本的SDK，您可以根据开发需要到各语言的对应仓库中寻找适用版本。

## Java SDK

在Maven项目文件中添加以下依赖：

```
  <dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>cs20151215</artifactId>
    <version>[2.4.1,)</version>
  </dependency>
```

## Go SDK

推荐使用go工具进行安装：

```
go get github.com/alibabacloud-go/cs-20151215/v2/client
```

## .NET SDK

推荐使用dotnet工具进行安装：

```
dotnet add package AlibabaCloud.SDK.CS20151215
```

## PHP SDK

推荐使用composer工具进行安装：

```
composer require alibabacloud/cs-20151215
```

## TypeScript/Node.js SDK

推荐使用NPM工具进行安装：

```
npm install @alicloud/cs20151215 --save
```

## Python SDK

推荐使用PyPI工具进行安装：

```
pip install alibabacloud_cs20151215
```

