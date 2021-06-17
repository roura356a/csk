---
keyword: [CIS加固, Alibaba Cloud Linux 2]
---

# ACK CIS加固使用说明

您可以在ACK集群中启用CIS加固功能，提升ACK集群所有节点的操作系统的安全性。本文介绍ACK基于Alibaba Cloud Linux 2实现CIS安全加固以及如何配置CIS安全加固后的检查。

CIS（Center for Internet Security）是一个第三方安全组织，致力于采用线上社区的模式与大公司、政府机构、学术机构一起打造优秀的安全实践解决方案。当前各大个公司发布的Linux操作系统大多都已经提供CIS Benchmark，包括Alibaba Cloud Linux 2、CentOS、Ubuntu等。当前发布CIS Benchmark已经成为很多阿里云客户对于操作系统安全的重要评判依据之一。更多信息，请参见[CIS WorkBench](https://workbench.cisecurity.org/)。

Alibaba Cloud Linux 2不仅是阿里云官方操作系统镜像，也是ACK的首选默认系统镜像。Alibaba Cloud Linux 2在2019年8月16日正式通过了CIS组织的全部认证流程并对外发布CIS Aliyun Linux 2 Benchmark version 1.0.0。更多信息，请参见[CIS Aliyun Linux 2 Benchmark version 1.0.0](https://www.cisecurity.org/benchmark/Aliyun_Linux/)。

## CIS Aliyun Linux 2 Benchmark version 1.0.0加固项分类说明

CIS Aliyun Linux 2 Benchmark version 1.0.0是最新的CIS Aliyun Linux 2 Benchmark。您可以对其进行下载，具体操作，请参见[下载CIS Aliyun Linux 2 Benchmark version 1.0.0](#section_s8g_7iq_4j9)。CIS Aliyun Linux 2 Benchmark version 1.0.0一共包含204项，按照每一项所处的安全等级分为Level 1（168项）和Level 2（36项）。其中Level 1与Level 2的主要区别如下：

-   Level 1是说明此加固条目是基础项加固且不会带来较大的性能影响。
-   Level 2说明此条目适用于安全性更高的场景，且可能会对系统带来性能开销。

此外CIS Aliyun Linux 2 Benchmark从计分（Scoring Information）的维度分为了Scored和Not Scored两类：

-   Scored：意味着此条目会纳入计分项，如果验证系统是安全的，则加分，如果不安全，则减分。
-   Not Scored：意味着无论是否安全，都不纳入计分项，也就是说不增减分。

综上，CIS Aliyun Linux 2一共204项，可以分为四类：

-   Level 1 Scored（共145项）
-   Level 1 Not Scored（共21项）
-   Level 2 Scored（共33项）
-   Level 2 Not Scored（共3项）

因为Level 2可能会影响性能且Not Scored项不纳入计分，因此对节点的Alibaba Cloud Linux 2操作系统，**ACK集群只针对Level 1 Sorced项进行安全加固**。

## 使用Alibaba Cloud Linux 2 CIS加固版

在创建ACK集群时，您可以选择启用CIS加固，这样集群在创建时会自动配置对应的CIS加固项，使得ACK集群所有节点的Alibaba Cloud Linux 2镜像满足CIS Aliyun Linux 2 Benchmark version 1.0.0中大部分Level 1 Scored项的加固要求。关于具体满足的加固项，请参见[ACK CIS加固集群满足的CIS Level 1 Scored加固项清单](#section_qq6_5no_2nz)。

**说明：** 为了满足CIS Level 1的加固要求，ACK会在CIS加固的Alibaba Cloud Linux 2操作系统中默认创建**ack\_cis**这个普通用户。

## ACK CIS加固集群满足的CIS Level 1 Scored加固项清单

经过对CIS Aliyun Linux 2 Benchmark version 1.0.0所有Level 1 Scored（共145项）进行分析和测试，容器服务ACK团队对除下表所列的17项以外的128项（88%以上）进行了安全加固。

|加固项|未加固的原因|
|---|------|
|1.1.2 Ensure /tmp is configured \(Scored\)|涉及到分区的修改。|
|1.1.18 Ensure sticky bit is set on all world-writable directories \(Scored\)|影响ACK管控逻辑。|
|1.7.1.1 Ensure message of the day is configured properly \(Scored\)|需要删除Alibaba Cloud Linux 2系统MOTD中的使用指南的链接。|
|3.1.1 Ensure IP forwarding is disabled \(Scored\)|对ACK的网络组件有影响。|
|3.5.1.1 Ensure default deny firewall policy \(Scored\)|需要您设置自己的防火墙策略。|
|3.5.1.2 Ensure loopback traffic is configured \(Scored\)|需要您设置自己的Loopback规则。|
|3.5.1.4 Ensure firewall rules exist for all open ports \(Scored\)|需要您对开放的端口设置防火墙策略。|
|3.5.2.1 Ensure IPv6 default deny firewall policy \(Scored\)|需要您设置自己的IPv6防火墙策略。|
|3.5.2.2 Ensure IPv6 loopback traffic is configured \(Scored\)|需要您设置自己的IPv6 Loopback规则。|
|4.2.1.4 Ensure rsyslog is configured to send logs to a remote log host \(Scored\)|需要您配置Rsyslog来设置自己的远程Log host。|
|4.2.3 Ensure permissions on all logfiles are configured \(Scored\)|改动文件太多，存在潜在风险。|
|5.2.10 Ensure SSH root login is disabled \(Scored\)|需要您自行创建其它登录账号或者选择VNC等非SSH登录方式，然后才能禁用Root登录。|
|5.2.18 Ensure SSH access is limited \(Scored\)|需要您配置允许登录的用户和组。|
|5.2.3 Ensure permissions on SSH private host key files are configured \(Scored\)|扫描脚本有误，`ssh_keys`的GID被硬编码为998， 但实际系统不一定是998（可能是996等）。|
|5.3.2 Ensure lockout for failed password attempts is configured \(Scored\)|Benchmark给出的修复建议跟Alibaba Cloud Linux 2系统对应的配置文件差异较大，建议谨慎修改。|
|6.1.11 Ensure no unowned files or directories exist \(Scored\)|影响ACK管控逻辑。|
|6.1.12 Ensure no ungrouped files or directories exist \(Scored\)|影响ACK管控逻辑。|

您可以参考CIS Aliyun Linux 2 Benchmark version 1.0.0手册中的以下条目，修复ACK集群中没修复的CIS Level 1 Scored项。根据以下内容中的**Remediation**部分进行修复，然后按照**Audit**检查您的修复是否生效。

|条目|描述|
|--|--|
|Profile Applicability|该项属于Level 1还是Level 2。|
|Description|加固条目的简单介绍。|
|Rationale|用于描述条目的细节和背景，告诉读者这么加固的意义和原因。|
|Audit|关键项，用于判断检测系统是否达标的脚本。根据此脚本的运行返回值来判断是否需要加固。|
|Remediation|关键项，如果Audit环节判断系统需要进行加固，那此环节就是执行脚本进行安全处理。|
|Impact|影响，主要来描述如果不进行正确配置可能会导致的影响。|
|References|参考文献。|
|CIS Controls|此条目对应的CIS control文档的讲解，需要注册后才能下载。|

## 下载CIS Aliyun Linux 2 Benchmark version 1.0.0

1.  登录并访问[CIS Benchmark官方主页](https://www.cisecurity.org/cis-benchmarks/)。

2.  同时选中**Operating Systems**和**Linux**。

    ![CIS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7616651261/p276039.png)

3.  找到Aliyun Linux CIS Benchmark，并单击**Download CIS Benchmark**。

    ![CIS 2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7616651261/p276040.png)

4.  在CIS下载界面完成填写个人的基本信息，然后单击**Get Free Benchmarks Now**。

    ![CIS 3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7616651261/p276046.png)

5.  等待几分钟后，您填写的邮箱会收到CIS的邮件，单击邮件中的**Access PDFs**。

    ![CIS 4](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7616651261/p276076.png)

6.  在下载页面找到最近访问的CIS Aliyun Linux 2 Benchmark v1.0.0并单击**Download PDF**。

    ![PDF](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6856651261/p276095.png)


## 使用CIS-CAT扫描工具验证ACK集群的CIS加固效果

如果您想验证ACK集群的CIS加固效果，可以使用CIS官网提供的CIS-CAT工具进行扫描验证。CIS-CAT是一个安全配置评估软件工具，即扫描工具，它能够详细地提供目标系统的评估结果。通过运行该工具，可以得到目标系统在指定CIS Benchmark profile的合规分数，同时还会针对不合规的配置给出修复步骤。更多信息，请参见[CIS-CAT工具](https://www.cisecurity.org/cybersecurity-tools/)。

CIS-CAT分为Lite和Pro版，Lite提供的功能有限，且仅支持的系统包括Windows 10, Ubuntu 18.04和Google Chrome等，不支持Alibaba Cloud Linux 2操作系统，因此无法扫描ACK集群的CIS加固效果。

CIS-CAT Pro具有v4和v3两个版本系列。以v4为例，本文将阐述如何使用CIS-CAT Pro进行扫描验证ACK集群的CIS加固效果。

1.  登录[CIS SecureSuite](https://enroll.cisecurity.org/memberships/#/)，然后注册成为CIS SecureSuite会员，并下载CIS-CAT Pro工具包（Assessor-CLI-v4.0.23.zip）。

2.  登录到ACK集群的任一Alibaba Cloud Linux 2操作系统的节点。

    关于连接ACK集群ECS节点的具体方式，请参见[查看节点列表](/intl.zh-CN/Kubernetes集群用户指南/节点与节点池/节点/查看节点列表.md)和[连接方式概述](/intl.zh-CN/实例/连接实例/连接方式概述.md)。

3.  分别执行以下命令安装CIS-CAT需要的Java环境。

    ```
    yum -y install java-1.8.0-openjdk java-1.8.0-openjdk-devel
    ```

    ```
    cat > /etc/profile.d/java8.sh <<EOF 
    export JAVA_HOME=$(dirname $(dirname $(readlink $(readlink $(which javac)))))
    export PATH=$PATH:$JAVA_HOME/bin
    export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar
    EOF
    ```

    ```
    source /etc/profile.d/java8.sh
    ```

4.  分别执行以下命令使用CIS-CAT Pro工具（Assessor-CLI-v4.0.23.zip）进行扫描。

    ```
    unzip Assessor-CLI-v4.0.23.zip
    ```

    ```
    cd Assessor-CLI
    ```

    ```
    chmod +x ./Assessor-CLI.sh
    ```

    ```
    ./Assessor-CLI.sh  -b ./benchmarks/CIS_Aliyun_Linux_2_Benchmark_v1.0.0-xccdf.xml  -p "Level 1" -html
    ```

    **说明：**

    -   `-b`：用来选择Benchmark的类型（操作系统和对应的版本类型）。
    -   `-p`：用来选择扫描的Level，因ACK只对CIS Level 1 Scored进行扫描，因此扫描时选择Level 1。
5.  查看扫描结果。

    ![CIS 扫描结果](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5115851261/p276400.png)

    扫描结果解释如下。更多信息，请参见[CIS-CAT Pro Assessor v4 Report](https://ccpa-docs.readthedocs.io/en/latest/User%20Guide%20-%20Assessor/#report-about-the-html-report)。

    |参数|解释|
    |--|--|
    |Total \# of Results|所选择的Benchmark的加固项总数，CIS\_Aliyun\_Linux\_2\_Benchmark\_v1.0.0总共204项。|
    |Total Scored Results|所选Level的Scored项总数，Level 1一共145项。|
    |Total Pass|所选Level的Scored项中当前系统通过的总数，CIS加固的ACK集群每个节点通过的Level 1 Scored项为128项。|
    |Total Fail|所选Level的Scored项中当前系统未通过的总数，CIS加固的ACK集群每个节点未通过的Level 1 Scored项为17项。|
    |Total Error|所选Level的Scored项中扫描脚本执行出错的项，这里CIS-CAT Pro都正确返回，因此结果为0。|
    |Total Unknown|所选Level的Scored项中，CIS-CAT无法判断是否满足安全加固标准的项，这里结果为0。|
    |Total Not Applicable|所选的Benchmark中与当前操作系统不匹配的项。在Alibaba Cloud Linux 2上执行CIS\_Aliyun\_Linux\_2\_Benchmark\_v1.0.0的CIS-CAT Pro扫描不存在不匹配的项。|
    |Total Not Checked|与Total Informational一样同属于Not Scored。|
    |Total Not Selected|所选Benchmark中未检查的加固项。CIS-CAT Pro对Level 1进行检查，因此这里未检查的加固项为所有的Level 2（共36项）。|
    |Total Informational|需要手动评估是否满足，在所选Level中属于Not Scored。|


**相关文档**  


[Alibaba Cloud Linux 2概述](/intl.zh-CN/镜像/Alibaba Cloud Linux 2/Alibaba Cloud Linux 2概述.md)

[CIS Aliyun Linux 2 Benchmark version 1.0.0](https://www.cisecurity.org/benchmark/Aliyun_Linux/)

[CIS-CAT工具](https://www.cisecurity.org/cybersecurity-tools/)

[CIS-CAT Pro Assessor v4 Report](https://ccpa-docs.readthedocs.io/en/latest/User%20Guide%20-%20Assessor/#report-about-the-html-report)

