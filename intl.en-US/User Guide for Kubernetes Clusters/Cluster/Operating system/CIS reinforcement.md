---
keyword: [CIS reinforcement, Alibaba Cloud Linux 2]
---

# CIS reinforcement

You can enable Center for Internet Security \(CIS\) reinforcement to enhance the security of the operating systems of nodes in a Container Service for Kubernetes \(ACK\) cluster. This topic describes how ACK implements CIS reinforcement based on the Alibaba Cloud Linux 2 operating system and how to assess CIS Benchmark configuration recommendations.

CIS is a third-party security organization that is committed to leading a global community of businesses, public service sectors, and academia to develop security best practice solutions. CIS provides CIS Benchmarks for the Linux-based operating systems released by major companies, such as Alibaba Cloud Linux 2, CentOS, and Ubuntu. CIS Benchmarks have become a critical criterion for assessing OS security for many Alibaba Cloud customers. For more information, see [CIS WorkBench](https://workbench.cisecurity.org/).

Alibaba Cloud Linux 2 is the official OS image developed by Alibaba Cloud and the default OS image used in ACK clusters. Alibaba Cloud Linux 2 passed the certification procedure of CIS on August 16, 2019. CIS then released CIS Aliyun Linux 2 Benchmark version 1.0.0. For more information, see [CIS Aliyun Linux 2 Benchmark version 1.0.0](https://www.cisecurity.org/benchmark/Aliyun_Linux/).

## CIS Aliyun Linux 2 Benchmark

The latest version of CIS Aliyun Linux 2 Benchmark is V1.0.0. To download CIS Aliyun Linux 2 Benchmark, see [Download CIS Aliyun Linux 2 Benchmark version 1.0.0](#section_s8g_7iq_4j9). CIS Aliyun Linux 2 Benchmark version 1.0.0 consists of 204 items that are classified into two security levels. Level 1 contains 168 items, and Level 2 contains 36 items. Differences between Level 1 and Level 2 items:

-   Level 1 items are used to implement basic improvements. These items do not have a large impact on system performance.
-   Level 2 items are suitable for scenarios that require high security. These items may increase performance overhead.

Besides, CIS Aliyun Linux 2 Benchmark classifies the items into two groups based on scoring information: Scored and Not Scored.

-   Scored: Compliance with Scored items increases the final benchmark score. Failure to comply with Scored items decreases the final benchmark score.
-   Not Scored: Compliance with Not Scored items does not increase the final benchmark score. Failure to comply with Not Scored items does not decrease the final benchmark score.

Therefore, the 204 items of CIS Aliyun Linux 2 Benchmark can be classified into four groups:

-   Level 1 Scored \(145 items\)
-   Level 1 Not Scored \(21 items\)
-   Level 2 Scored \(33 items\)
-   Level 2 Not Scored \(3 items\)

Level 2 items may negatively impact system performance and Not Scored items do not affect the final benchmark score. Therefore, **ACK provides reinforcement for only Level 1 Scored items**.

## Enable CIS reinforcement

You can choose to enable CIS reinforcement when you create an ACK cluster. This way, the system automatically configures CIS reinforcement for the cluster. This ensures that the Alibaba Cloud Linux 2 images of all nodes in the cluster meet most requirements of Level 1 Scored items in CIS Aliyun Linux 2 Benchmark version 1.0.0. For more information about the required items, see [CIS Level 1 Scored items that are covered by CIS reinforcement](#section_qq6_5no_2nz).

**Note:** To meet the requirements of Level 1 items, ACK automatically creates a normal user named **ack\_cis** in the Alibaba Cloud Linux 2 operating system for which CIS reinforcement is enabled.

## CIS Level 1 Scored items that are covered by CIS reinforcement

CIS Aliyun Linux 2 Benchmark version 1.0.0 contains 145 Level 1 Scored items. Based on analysis and testing of these items, ACK provides CIS reinforcement for 128 out of the 145 items. The coverage is more than 88%.

|Item|Reason why the item is not covered by CIS reinforcement|
|----|-------------------------------------------------------|
|1.1.2 Ensure /tmp is configured \(Scored\)|Involves partition modifications.|
|1.1.18 Ensure sticky bit is set on all world-writable directories \(Scored\)|Affects the control logic of ACK.|
|1.7.1.1 Ensure message of the day is configured properly \(Scored\)|Requires the deletion of the link to the user guide in the Message of the Day \(MOTD\) of Alibaba Cloud Linux 2 operating system.|
|3.1.1 Ensure IP forwarding is disabled \(Scored\)|Affects the networking component of ACK.|
|3.5.1.1 Ensure default deny firewall policy \(Scored\)|Requires the configuration of firewall policies.|
|3.5.1.2 Ensure loopback traffic is configured \(Scored\)|Requires the configuration of loopback rules.|
|3.5.1.4 Ensure firewall rules exist for all open ports \(Scored\)|Requires the configuration of firewall rules for open ports.|
|3.5.2.1 Ensure IPv6 default deny firewall policy \(Scored\)|Requires the configuration of IPv6 firewall policies.|
|3.5.2.2 Ensure IPv6 loopback traffic is configured \(Scored\)|Requires the configuration of IPv6 loopback rules.|
|4.2.1.4 Ensure rsyslog is configured to send logs to a remote log host \(Scored\)|Requires the configuration of rsyslog to send log data to a remote log host.|
|4.2.3 Ensure permissions on all logfiles are configured \(Scored\)|Requires the modification of a large number of files, which results in potential security risks.|
|5.2.10 Ensure SSH root login is disabled \(Scored\)|Requires the creation of other accounts for authentication or the use of non-SSH connections, such as Virtual Network Computing \(VNC\) connections.|
|5.2.18 Ensure SSH access is limited \(Scored\)|Requires the configuration of users and groups that are allowed to access the system by using SSH.|
|5.2.3 Ensure permissions on SSH private host key files are configured \(Scored\)|The GID of `ssh_keys` is hard-coded to 998 in the scan script. However, the GID may not be 998 in the system. The GID may be 996.|
|5.3.2 Ensure lockout for failed password attempts is configured \(Scored\)|The Benchmark configuration recommendations are quite different from the configuration file of the Alibaba Cloud Linux 2 system. We recommend that you proceed with caution.|
|6.1.11 Ensure no unowned files or directories exist \(Scored\)|Affects the control logic of ACK.|
|6.1.12 Ensure no ungrouped files or directories exist \(Scored\)|Affects the control logic of ACK.|

You can refer to the following sections in CIS Aliyun Linux 2 Benchmark version 1.0.0 to add fixes for CIS Level 1 Scored items that are not covered by CIS reinforcement. You can add fixes based on the **Remediation** section and check whether the fix works as expected based on the **Audit** section.

|Section|Description|
|-------|-----------|
|Profile Applicability|Whether the item belongs to Level 1 or Level 2.|
|Decription|The brief introduction of the item.|
|Rationale|The details and background information about the item. This helps you understand the reason for the recommended reinforcement.|
|Audit|The command script that is used to check whether the system meets the criteria. You can determine whether reinforcement is required based on the return value of the script.|
|Remediation|If the script in the Audit section indicates that reinforcement is required, you can run this script to reinforce the system.|
|Impact|Possible impacts if the system is not properly configured.|
|References|References.|
|CIS Controls|The description of the CIS control that corresponds to the item. To download CIS Controls, you must create an account.|

## Download CIS Aliyun Linux 2 Benchmark version 1.0.0

1.  Log on to the [CIS Benchmark homepage](https://www.cisecurity.org/cis-benchmarks/).

2.  Select **Operating Systems** and **Linux**.

    ![CIS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8212183261/p276039.png)

3.  Find Aliyun Linux and click **Download CIS Benchmark**.

    ![CIS 2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8212183261/p276040.png)

4.  On the download page that appears, enter your basic information and click **Get Free Benchmarks Now**.

    ![CIS 3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8212183261/p276046.png)

5.  Wait a few minutes. Check your email inbox and find the email from CIS. Click **Access PDFs** in the email.

    ![CIS 4](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8212183261/p276076.png)

6.  On the download page, find CIS Aliyun Linux 2 Benchmark v1.0.0 and click **Download PDF**.

    ![PDF](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8212183261/p276095.png)


## Use CIS-CAT to evaluate the compliance of an ACK cluster with the CIS Benchmark

To evaluate the compliance of an ACK cluster with the CIS Benchmark, you can use CIS-Configuration Assessment Tool \(CAT\) to scan the cluster. CIS-CAT is a configuration assessment tool that scans the configuration of a system to provide a detailed evaluation report. You can run this tool on a system to obtain a benchmark score against a specified CIS Benchmark profile. The tool also provides remediation steps for noncompliant configurations. For more information, see [CIS-CAT](https://www.cisecurity.org/cybersecurity-tools/).

CIS-CAT has two editions: Lite and Pro. CIS-CAT Lite provides limited features and supports only the following systems: Windows 10, Ubuntu 18.04, and Google Chrome. CIS-CAT Lite does not support Alibaba Cloud Linux 2 and therefore cannot be used to scan ACK clusters for compliance evaluation.

CIS-CAT Pro has two versions: v4 and v3. The following section shows how to use CIS-CAT Pro v4 to scan an ACK cluster to evaluate the compliance of the cluster with the CIS Benchmark.

1.  Go to [CIS SecureSuite](https://enroll.cisecurity.org/memberships/#/) and register a CIS SecureSuite membership. Then, download the CIS-CAT Pro installation package named Assessor-CLI-v4.0.23.zip.

2.  Log on to a cluster node that runs Alibaba Cloud Linux 2.

    For more information about how to connect to an Elastic Compute Service \(ECS\) node in an ACK cluster, see [View nodes](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/View nodes.md) and [Overview](/intl.en-US/Instance/Connect to instances/Overview.md).

3.  Run the following commands in sequence to install a Java environment that is required by CIS-CAT:

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

4.  Run the following commands in sequence to use CIS-CAT Pro to scan the node:

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

    **Note:**

    -   `-b`: specifies the benchmark based on which the node is scanned. The parameter value includes the operating system and benchmark version.
    -   `-p`: specifies the level of items that are scanned. In this example, Level 1 is specified because only CIS Level 1 Scored items need to be scanned.
5.  Check the scan result.

    ![CIS scan result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8212183261/p276400.png)

    The following table describes the parameters in the scan result. For more information, see [CIS-CAT Pro Assessor v4 Report](https://ccpa-docs.readthedocs.io/en/latest/User%20Guide%20-%20Assessor/#report-about-the-html-report).

    |Parameter|Description|
    |---------|-----------|
    |Total \# of Results|The total number of items that are provided by the specified benchmark. CIS Aliyun Linux 2 Benchmark v1.0.0 contains 204 items.|
    |Total Scored Results|The total number of Scored items that belong to the specified level. Level 1 contains 145 items.|
    |Total Pass|The total number of Scored items that belong to the specified level and passed the check. ACK provides CIS reinforcement for 128 Level 1 Scored items.|
    |Total Fail|The total number of Scored items that belong to the specified level and failed the check. ACK does not provide CIS reinforcement for 17 Level 1 Scored items.|
    |Total Error|The total number of Scored items that belong to the specified level and caused errors during script execution. In this example, no error occurred and therefore the result is 0.|
    |Total Unknown|The total number of Scored items that belong to the specified level and where CIS-CAT was unable to determine if the criteria were met. In this example, the result is 0.|
    |Total Not Applicable|The total number of items of the specified benchmark that are not applicable to the operating system. When you use CIS-CAT Pro to scan a node that runs Alibaba Cloud Linux 2 against CIS Aliyun Linux 2 Benchmark v1.0.0, all items apply.|
    |Total Not Checked|These items are Not Scored. The items that belong to the Total Informational category are also Not Scored.|
    |Total Not Selected|The total number of items of the specified benchmark that are not checked. In this example, CIS-CAT Pro checks only Level 1 items. Therefore, the 36 Level 2 items are not checked.|
    |Total Informational|The total number of items that require manual evaluation. These items are Not Scored in the specified level.|


**Related topics**  


[Overview](/intl.en-US/Images/Alibaba Cloud Linux 2/Overview.md)

[CIS Aliyun Linux 2 Benchmark version 1.0.0](https://www.cisecurity.org/benchmark/Aliyun_Linux/)

[CIS-CAT](https://www.cisecurity.org/cybersecurity-tools/)

[CIS-CAT Pro Assessor v4 Report](https://ccpa-docs.readthedocs.io/en/latest/User%20Guide%20-%20Assessor/#report-about-the-html-report)

