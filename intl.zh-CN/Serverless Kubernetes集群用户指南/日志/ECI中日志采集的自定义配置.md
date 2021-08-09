ECI中日志采集的自定义配置 
===================================



用户自定义设置 
----------------------------

根据业务需要，您可能需要将ECI的日志收集到自定义项目下的自定义日志库里。对于不同的应用和服务，您可能还需要将ECI实例加入不同的机器组。对于项目、日志库和机器组等自定义配置需求，您可以通过两种办法实现：

* **通过日志服务控制台（API）手动设置**

  




用户可以自行登录日志服务控制台，创建自定义项目，创建自定义日志库，以及自定的机器组，为日志库创建自定义config并应用到选择的机器组。这样日志内容就可以导向新的日志库了。

如果觉得通过日志服务控制台配置太繁琐，您可以通过ECI代创建和配置。

* **通过ECI自定义**

  




ECI除了具备为用户生成所有默认设置外，还支持为用户生成自定义的配置。比如项目名、日志库名、配置名、机器组名、以及日志收集目录等。具体的参数通过ECI的容器的环境变量传入，格式如下：

**Logstore名和配置名** 

首先是配置名

```javascript
-name: aliyun_logs_{配置名}
-value: {日志采集路径}
```


**注意**

如需采集标准输出，请将日志采集路径设置为stdout。



默认情况下，logstore的名字和配置名同名，如果需要设置配置所输出的logstore的名字，可以采用如下的方式自定义：

```unknow
-name: aliyun_logs_{配置名}_logstore
-value: {logstore 名称}
```





**日志库名约束** 

* 日志库名称仅支持小写字母、数字、连字符（-）和下划线（_）。

  

* 必须以小写字母和数字开头和结尾。

  

* 名称长度为3-63个字符。

  



**注意**

校验不通过的，会直接忽略，使用ECI默认的。



**项目名** 

设置日志收集所属的project，方式如下。

```unknow
-name: aliyun_logs_{配置名}_project
-value: {project 名称}
```



默认情况下，对于ECI的API用户，每个地域会有一个默认的project，对于k8s的用户，默认project为每个集群一个，命名方式为"k8s-log-{k8s集群id}"

**项目名约束** 

* 项目名称仅支持小写字母、数字和连字符（-）。

  

* 必须以小写字母和数字开头和结尾。

  

* 名称长度为3-63个字符。

  



**注意**

校验不通过的，会直接忽略，使用ECI默认的。



**Logstore设置分区数** 

什么是分区（Shard），请参见[分区](/intl.zh-CN/产品简介/基本概念/分区（Shard）.md)。

设置方法： 



```javascript
-name: aliyun_logs_{配置名}_shard
-value: {shard数值}
```



默认值为2，可选范围是\[1,10\]。



**Logstore设置日志保留时间** 

设置方法： 



```javascript
-name: aliyun_logs_{配置名}_ttl
-value: {ttl数值}
```



默认值为90，可选范围是\[1,3650\]。



**机器组名称** 

非必填参数，

默认情况，

对于ECI API 用户，ECI实例会加入到ECI帮用户创建的默认机器组，一个region对应一个。

对于 kubernetes 用户，ECI实例会加入集群默认的机器组，命名格式"k8s-group-{k8s集群id}"。

自定义设置的格式如下：

```javascript
-name: aliyun_logs_{配置名}_machinegroup
-value: {机器组名称}
```





**机器组名约束** 

* 机器组名称仅支持字母、数字、连字符（-）和下划线（_）。

  

* 必须以小写字母和数字开头和结尾。

  

* 名称长度为3-63个字符。

  





**注意**

校验不通过的，会直接忽略，使用ECI默认的。

用户Volume日志收集 
---------------------------------

对于标准输出和错误输出，只需要通过环境变量进行设置就可以收集；如需要收集任意的自定的目录下的日志文件，需要依赖Volume才可以进行收集。

Volume的标准日志收集目录为Volume挂载的目录下的子目录，具体取决于用户自己的设定。

比如：

用户有个EmptyDirVolume，挂载到了容器的/pod/data/目录下，那么Volume的日志收集可以指定是/pod/data/下的任意子目录下的任意文件。通过这种方式，用户可以灵活的调整挂载目录并配合自己的业务，实现自定义的日志收集目录。



**创建EmptyDirVolume** 



```unknow
'Volume.1.Name': 'default-volume',
'Volume.1.Type': 'EmptyDirVolume',
```



**将Volume挂载至容器目录** 



```unknow
'Container.1.VolumeMount.1.Name': 'default-volume',
'Container.1.VolumeMount.1.MountPath': '/pod/data/',
'Container.1.VolumeMount.1.ReadOnly': False,
```



**配置日志仓库** 

'aliyun_logs_stdout-test' 为ECI的容器的标准输出的收集目录，'aliyun_logs_log-file' 为Volume的日志收集目录，模糊匹配/pod/data/目录下的任意日志文件。




```unknow
'Container.1.EnvironmentVar.1.Key': 'aliyun_logs_log-file',
'Container.1.EnvironmentVar.1.Value': '/pod/data/*.log',
'Container.1.EnvironmentVar.2.Key': 'aliyun_logs_stdout-test',
'Container.1.EnvironmentVar.2.Value': 'stdout',
'Container.1.EnvironmentVar.3.Key': 'aliyun_logs_log-file_project',
'Container.1.EnvironmentVar.3.Value': 'k8s-eci-project',
'Container.1.EnvironmentVar.4.Key': 'aliyun_logs_stdout-test_project',
'Container.1.EnvironmentVar.4.Value': 'k8s-eci-project',
'Container.1.EnvironmentVar.5.Key': 'aliyun_logs_log-file_machinegroup',
'Container.1.EnvironmentVar.5.Value': 'k8s-eci-mg',
'Container.1.EnvironmentVar.6.Key': 'aliyun_logs_stdout-test_machinegroup',
'Container.1.EnvironmentVar.6.Value': 'k8s-eci-mg',
```





**效果** 

![sls](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4539819951/p133041.png)

标准输出日志：

![stdin log](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4539819951/p133042.png)

日志文件收集：

![logfile](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4539819951/p133043.png)

