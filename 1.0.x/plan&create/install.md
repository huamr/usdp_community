# USDP社区版部署指南



### <center>[环境准备](usdp_community/1.0.x/plan&create/install?id=_1-环境准备)   |   [部署说明](usdp_community/1.0.x/plan&create/install?id=_2-部署说明)   |   [附录](usdp_community/1.0.x/plan&create/install?id=_3-附录)</center>  



## 1. 环境准备

用户进行如下简单步骤，即可利用 USDP 实现大数据业务、数据仓库等相关业务建设，简单有效。

在大数据生产环境中，几乎不可能使用伪分布式的单机部署模式，因此 USDP 智能大数据平台所支持的最小部署模式为 3 个节点，每个节点的配置情况，需要根据用户业务的使用情况而定，例如：每个节点上部署 10 个服务、组件与部署 20 个服务、组件所消耗的 CPU、内存资源必然不同。按照最低运行标准，USDP 给出如下最低参考配置：

### 1.1 最低配置（产品体验）：

| 节点序号 | CPU   | 内存  | 系统盘 | 数据盘  | 系统             |
| -------- | ----- | ----- | ------ | ------- | ---------------- |
| 节点1    | 4 核+ | 8 GB+ | 80 GB+ | 500 GB+ | CentOS 7.2 - 7.6 |
| 节点2    | 4 核+ | 8 GB+ | 80 GB+ | 500 GB+ | CentOS 7.2 - 7.6 |
| 节点3    | 4 核+ | 8 GB+ | 80 GB+ | 500 GB+ | CentOS 7.2 - 7.6 |

 注意：上述节点可以是物理节点、也可以是虚拟节点。

若期望构建更大规模、更加复杂的大数据分析系统，可前往 [资源规划参考](usdp_community/1.0.x/plan&create/install) 进行准备。

### 1.2 下载资源与系统环境准备

节点配置完成后，用户即可开始下载对应的内容，进行部署前的准备工作了。

#### 1.2.1 USDP社区版安装包下载

点击 [USDP社区版获取](usdp_community/1.0.x/plan&create/download) 进行下载。

#### 1.2.2 资源说明

USDP 的下载内容主要分为如下 3 种类型：

| 类型序号 | 安装包名称                                       | 安装包说明                    | 放置目录       |
| -------- | ------------------------------------------------ | ----------------------------- | -------------- |
| 1        | usdp-01-master-privatization-free-1.0.0.0.tar.gz | USDP 主程序与大数据服务资源包 | /opt/usdp-srv/ |
| 2        | httpd-rpms.tar.gz 、mirror.tgz                   | USDP 离线 yum 基础源资源包    | /data          |
| 3        | epel.tgz                                         | USDP 离线 yum 扩展源资源包    | /data          |

其中压缩包 usdp-01-master-privatization-free-1.0.0.0.tar.gz 解压后，得到如下目录：

| 序号 | 目录         | 目录说明                     |
| ---- | ------------ | ---------------------------- |
| 1    | agent        | USDP 分布式客户端程序        |
| 2    | bin          | USDP 程序启停脚本            |
| 3    | config       | USDP 程序配置文件            |
| 4    | jmx_exporter | 进程监控指标采集程序         |
| 5    | recommend    | 大数据服务部署预置模板       |
| 6    | repair       | 部署前环境初始化脚本与资源包 |
| 7    | repository   | 大数据服务资源包             |
| 8    | scripts      | USDP 相关程序脚本            |
| 9    | server       | USDP 分布式管理端程序        |
| 10   | sql          | USDP 元数据信息初始化 SQL    |
| 11   | templated    | 大数据服务配置模板           |
| 12   | verify       | 证书存放路径                 |
| 13   | versions     | USDP 大数据资源包版本信息    |

## 2. 部署说明

在开始部署之前，请选择在准备部署 USDP 服务端的节点上通过 root 命令执行如下操作。

### 2.1 环境初始化

本篇主要介绍如何通过 USDP 自动初始化脚本完成集群环境配置，如果用户想要自己完成环境初始化工作，可以参考附录中的内容，手动安装依赖。

#### 2.1.1 首次全量修复

##### 1. 环境初始化模块目录说明

环境初始化模块的子目录如下：

| repair 初始化模块子目录 | 说明                                             |
| ----------------------- | ------------------------------------------------ |
| bin                     | 单个修复模块脚本所在目录，无需手动管理；         |
| config                  | 一键修复脚本所需配置文件目录，需要用户手动修改； |
| packages                | 修复过程中安装 USDP 所需依赖压缩包存放目录；     |
| sbin                    | 一键修复主脚本所在目录，无需手动管理；           |

##### 2. 修改配置文件

repair 修复模块子目录 config 下存放修复脚本所需配置文件，如下所示。

| 环境初始化模块配置文件          | 说明                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| repair.properties               | 主要配置私有化 yum 源安装节点信息、namp 安装节点信息、mysql 数据库安装节点信息、修复机器总数，以及修复模块日志存放位置。用户根据需要自行修改相关配置项； |
| repair-host-info.properties     | 节点全量修复，需要配置此文件，具体配置所有节点内网 Ip、密码、端口号以及主机名； |
| repair-host-info-add.properties | 集群新增节点时，需要配置此文件，具体配置新增节点内网 Ip、密码、端口号以及主机名； |

首次全量初始化环境所需修改配置文件如下所示。



•repair.properties

```shell
示例如下：
# Set the YUM source host IP
yum.repo.host.ip=127.0.0.1(need to change)
#The Host information for installing the NMAP service
namp.server.ip=127.0.0.1(need to change)
namp.server.port=22
namp.server.password=your-node-root-password(need to change)
# The Host information for installing the NTP service(Master)
ntp.master.ip=127.0.0.1(need to change)
# Install MySQL machine node information
mysql.ip=127.0.0.1(need to change)
mysql.host.ssh.port=22
mysql.host.ssh.password=your-mysql-password(need to change)
# Set the MYSQL database login password
mysql.password=ucloud.cn1qaz!QAZ(need to change)
# The total number of machines needed to be repaired.
repair.host.num=n(The total number of machines needed to be repaired )
# The total number of added machines needed to be repaired.
repair.add.host.num=m(The total number of added machines needed to be repaired)
# Common Settings.
repair.log.dir=./log
```

 上述代码解释如下：

| 具体配置项              | 说明                                                         |
| ----------------------- | ------------------------------------------------------------ |
| yum.repo.host.ip        | 填写未来即将部署 私有化yum 源 的节点的内网 IP（即执行 repair 脚本的节点 ip ）； |
| namp.server.ip          | 填写未来即将部署 USDP 管理端 的节点的内网 IP；               |
| namp.server.port        | 填写未来即将部署 USDP 管理端 的节点 SSH 端口号，默认22；     |
| namp.server.password    | 填写未来即将部署 USDP 管理端 的节点的密码；                  |
| ntp.master.ip           | 选择某个节点作为 NTP 时间同步master 服务器，填写内网 IP；    |
| mysql.ip                | 选择某个节点作为 MySQL 服务器，填写内网 IP；                 |
| mysql.host.ssh.port     | 设置 MySQL 所在节点的 SSH 端口号，默认 22；                  |
| mysql.host.ssh.password | 设置 MySQL 的 所在节点的密码；                               |
| mysql.password          | 设置数据库登录密码；                                         |
| repair.host.num         | 设置修复机器数量；                                           |
| repair.add.host.num     | 新增节点时需要配置此项，全量修复时无需修改；                 |
| repair.log.dir          | 设置环境初始化日志存放位置；                                 |



•repair-host-info.properties

```shell
 示例如下：
 # 1.Please provide the information of hosts needed to be repaired in the format specified below
 # 2.usdp.ip.i(eg:i=1,2,3.....):
 # 3.usdp.password.i:
 # 4.usdp.ssh.port.i:
 # 5.usdp.ssh.port.hostname.i:
 usdp.ip.1=127.0.0.1(need to change)
 usdp.password.1=your-node-root-password(need to change)
 usdp.ssh.port.1=22
 
 usdp.ip.2=127.0.0.1(need to change)
 usdp.password.2=your-node-root-password(need to change)
 usdp.ssh.port.2=22
 usdp.ssh.port.hostname.2=your-node-hostname(need to change)
 usdp.ip.3=127.0.0.1(need to change)
 usdp.password.3=your-node-root-password(need to change)
 usdp.ssh.port.3=22
 usdp.ssh.port.hostname.3=your-node-hostname(need to change)
```

上述代码解释如下：

| 具体配置项               | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| usdp.ip.i                | USDP 集群安装节点内网 Ip；（i 表示1-n 的取值，n 为集群机器总数）； |
| usdp.password.i          | USDP 集群安装节点密码；                                      |
| usdp.ssh.port.i          | USDP 集群安装节点端口号，默认 22；                           |
| usdp.ssh.port.hostname.i | USDP 集群安装节点主机名；                                    |



##### 3. 执行初始化脚本

完成上述步骤后，执行如下命令即可开始一键初始化任务。

```shell
cd /opt/usdp-srv/usdp/repair/sbin
bash repair.sh initAll
source /etc/profile
```

初始化过程为完全离线的方式，等待一段时间后，即可将所有对应节点的环境准备完毕。



##### 4. 为 USDP 配置 MySQL 数据库

修改/opt/usdp-srv/usdp/config/application-server.yml文件，找到 datasource 配置片段，修改前如下：

```shell
datasource:
		type: com.zaxxer.hikari.HikariDataSource
		#    driver-class-name: org.gjt.mm.mysql.Driver
		driver-class-name: com.p6spy.engine.spy.P6SpyDriver
		url: jdbc:p6spy:mysql://udp01:3306/db_udp?useUnicode=true&characterEncoding=utf-8&useSSL=false
		username: root
		password: 1qaz!QAZ
```

根据之前设置的 mysql 的安装所在节点主机名以及数据库登录密码，修改url 中的对应udp01及 password 的值，修改后如下：

```shell
datasource:
		type: com.zaxxer.hikari.HikariDataSource
		#    driver-class-name: org.gjt.mm.mysql.Driver
		driver-class-name: com.p6spy.engine.spy.P6SpyDriver
		url: jdbc:p6spy:mysql://pusdp_master1:3306/db_udp?useUnicode=true&characterEncoding=utf-8&useSSL=false
		username: root
		password: ucloud.cn
```



#### 2.1.2 集群新增节点初始化步骤

当集群部署完毕后，如需要新增节点，同样需要准备好部署环境，对于新增节点环境初始化， USDP 也提供一键式修复，需要做如下步骤：

##### •配置 repair-host-info-add.properties 文件

与4.1节中的全量修复类似， 修改 repair-host-info-add.properties 文件，具体示例如下：

```shell
# 1.Please provide the information of added hosts needed to be repaired in the format specified below
# 2.usdp.ip.i(eg:i=1,2,3.....):
# 3.usdp.password.i:
# 4.usdp.ssh.port.i:
# 5.usdp.ssh.port.hostname.i:
usdp.ip.1=127.0.0.1(need to change)
usdp.password.1=your-node-root-password(need to change)
usdp.ssh.port.1=22
usdp.ssh.port.hostname.1=your-node-hostname(need to change)

usdp.ip.2=127.0.0.1(need to change)
usdp.password.2=your-node-root-password(need to change)
usdp.ssh.port.2=22
usdp.ssh.port.hostname.2=your-node-hostname(need to change)

usdp.ip.3=127.0.0.1(need to change)
usdp.password.3=your-node-root-password(need to change)
usdp.ssh.port.3=22
usdp.ssh.port.hostname.3=your-node-hostname(need to change)
```



##### •执行新增节点初始化脚本

修改完上述配置文件，即可进入 sbin 目录执行如下修复命令

```shell
  cd /opt/usdp-srv/usdp/repair/sbin
  bash repair.sh  initSingle
```



#### 2.1.3 新增集群前的修复步骤

当集群部署完毕后，如需要新增集群，同样需要准备好部署环境，对于新增集群环境初始化， USDP 也提供一键式修复，需要做如下步骤：

##### •重新配置repair-host-info.properties文件

其中 ，USDP 需要把 udp-server 所在节点信息添加到新增集群节点一起，具体实列及解释如下

```shell
# 1.Please provide the information of hosts needed to be repaired in the format specified below
# 2.usdp.ip.i(eg:i=1,2,3.....):
# 3.usdp.password.i:
# 4.usdp.ssh.port.i:
# 5.usdp.ssh.port.hostname.i:
usdp.ip.1=127.0.0.1(need to change)
usdp.password.1=your-node-root-password(need to change)
usdp.ssh.port.1=22
usdp.ssh.port.hostname.1=your-node-hostname(need to change)

usdp.ip.2=127.0.0.1(need to change)
usdp.password.2=your-node-root-password(need to change)
usdp.ssh.port.2=22
usdp.ssh.port.hostname.2=your-node-hostname(need to change)

usdp.ip.3=127.0.0.1(need to change)
usdp.password.3=your-node-root-password(need to change)
usdp.ssh.port.3=22
usdp.ssh.port.hostname.3=your-node-hostname(need to change)
```

上述代码解释如下：

| udp-server 所在机器配置项 | 说明                                   |
| ------------------------- | -------------------------------------- |
| usdp.ip.1                 | udp-server 所在机器内网 Ip ；          |
| usdp.password.1           | udp-server 所在机器 登录密码；         |
| usdp.ssh.port.1           | udp-server 所在机器端口号，默认为 22； |
| usdp.ssh.port.hostname.1  | udp-server 集群安装节点主机名；        |



##### •重新配置 repair.properties 文件

如果需要重新指定新集群安装数据库，仅需要修改以下配置项，其他可保持不变

```shell
# Install MySQL machine node information
mysql.ip=127.0.0.1(need to change)
mysql.host.ssh.port=22
mysql.host.ssh.password=your-mysql-password(need to change)
# Set the MYSQL database login password
mysql.password=ucloud.cn1qaz!QAZ(need to change)
```

上述配置项如下所示：

| 具体配置项              | 说明                                                 |
| ----------------------- | ---------------------------------------------------- |
| mysql.ip                | 新增集群指定新的 MySQL 安装节点 IP；                 |
| mysql.host.ssh.port     | 新增集群指定新的 MySQL 安装节点 SSH 端口号，默认22； |
| mysql.host.ssh.password | 新增集群指定新的 MySQL 安装节点的登录密码；          |
| mysql.password          | 新增集群指定新的 MySQL 数据库密码；                  |



##### •执行新增集群初始化脚本

修改完上述配置文件，即可进入 sbin 目录执行如下修复命令

```shell
cd /opt/usdp-srv/usdp/repair/sbin
bash repair.sh  initAddCluster
```

注意：

1.在repair-host-info-add.properties文件中，仅需配置每次新增的节点信息即可，若存在已修复过的节点信息时，在下次运行“repair.sh initSingle”指令前，请清除。

2.jdk 安装在 /opt/module 下面，不允许随意删除，否则 java 环境失效。

3.数据库密码中不得包含 "@" 。

4.主机名设置不得包含 "_"，"-" 。

###  

### 2.2 启动 USDP 服务端程序

节点修复完成后，进入 USDP 管理端所在节点后，并进入 USDP 安装根目录，通过 root 用户执行如下命令，以启动 USDP 管理端服务：

```
bin/start-udp-server.sh
```



### 2.3 访问 USDP Web 页面

通过浏览器访问如下地址即可打开 USDP Web 页面：

http://:80



### 2.4 设置初始化密码

第一次访问 USDP Web 页面需要设置管理员密码，设置完毕后，即可进行下一步操作。



### 2.5 下载免费版证书

使用 USDP 免费版，用户需要生成 [免费版证书](usdp_community/1.0.x/plan&create/download?id=_2-license获取)。

下载得到证书后，无需解压，直接在 USDP 页面中上传证书即可。完成后，即可使用开始使用 USDP 部署、管理、运维集群节点以及大数据相关服务。



## 3. 附录



如果不采用上述自动环境初始化，则需要手动修复节点环境时，需要通过 root 用户在每个节点中进行如下操作。

•01、安装 yum 依赖

```shell
yum -y install ssh sshpass expect nmap ntp libxslt-devel psmisc PerlJSON MySQL-python xdg-utils redhat-lsb python2-rpm-macros python-rpm-macros python-devel cyrus-sasl python36-devel gcc-c++ Cython Six websocket-client ecdsa pytest-runner krb5-devel
```



•02、安装 MySQL 5.7

–安装 MySQL 主程序（略）

–配置 my.cnf

 修改 /etc/my.cnf ：

```shell
echo "max_connections=3600" >> /etc/my.cnf; \
echo "explicit_defaults_for_timestamp=true" >> /etc/my.cnf;
```



–进入 MySQL 控制台执行如下 SQL 语句

```sql
SELECT host,user,authentication_string,Grant_priv,Super_priv FROM mysql.user;UPDATE mysql.user SET Grant_priv='Y', Super_priv='Y' WHERE User='root';FLUSH PRIVILEGES;

UPDATE mysql.user SET Host = '%' where User = 'root';GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '${num1}' WITH GRANT OPTION;FLUSH PRIVILEGES;

CREATE DATABASE if not exists db_udp CHARACTER SET utf8 COLLATE utf8_general_ci;use db_udp;source ${UDP_SQL_PATH}/init_db_udp.sql;
```



•03、安装 JDK

–设置 Java 环境变量到 /etc/profile、~/.bash_profile 中

–配置 security.provider

```shell
JAVA_SECURITY_DIR="${JAVA_HOME}/jre/lib/security/java.security"

JAVA_SECURITY_ARGS_ARR[0]="security.provider.1=sun.security.provider.Sun"
JAVA_SECURITY_ARGS_ARR[1]="security.provider.2=sun.security.rsa.SunRsaSign"
JAVA_SECURITY_ARGS_ARR[2]="security.provider.3=com.sun.net.ssl.internal.ssl.Provider"
JAVA_SECURITY_ARGS_ARR[3]="security.provider.4=com.sun.crypto.provider.SunJCE"
JAVA_SECURITY_ARGS_ARR[4]="security.provider.5=sun.security.jgss.SunProvider"
JAVA_SECURITY_ARGS_ARR[5]="security.provider.6=com.sun.security.sasl.Provider"
JAVA_SECURITY_ARGS_ARR[6]="security.provider.7=org.jcp.xml.dsig.internal.dom.XMLDSigRI"
JAVA_SECURITY_ARGS_ARR[7]="security.provider.8=sun.security.smartcardio.SunPCSC"
JAVA_SECURITY_ARGS_ARR[8]="security.provider.9=org.bouncycastle.jce.provider.BouncyCastleProvider"

for element in ${JAVA_SECURITY_ARGS_ARR[@]}
do
	JAVA_SECURITY_ARGS="${JAVA_SECURITY_ARGS}${element}\n"
done

echo -e ${JAVA_SECURITY_ARGS} >> ${JAVA_SECURITY_DIR}
```



–配置 bcprov-jdk15on-1.56.jar

```shell
JAVA_BCPROV_JAR="${JDK_FOLDER_PATH}/bcprov-jdk15on-1.56.jar"
JAVA_BCPROV_DIR="${JAVA_HOME}/jre/lib/ext/"
cp -a ${JAVA_BCPROV_JAR} ${JAVA_BCPROV_DIR}
```



•04、修改 tmp.conf

 在 /usr/lib/tmpfiles.d/tmp.conf 中添加如下内容：

```shell
 x /tmp/*.pid
 x /tmp/hsperfdata*/*
 X /tmp/hsperfdata*
```

•05、关闭 SELINUX

•06、配置 root 与 hadoop 用户的 SSH 免密登录

•07、关闭 Firewalls

•08、关闭 Swap

•09、修改 system.conf

修改 /etc/systemd/system.conf 文件：

```shell
sed -i '/DefaultLimitNOFILE=/d' /etc/systemd/system.conf
sed -i '/DefaultLimitNPROC=/d' /etc/systemd/system.conf

cat << eof="">> /etc/systemd/system.conf
DefaultLimitNOFILE=1024000
DefaultLimitNPROC=1024000
EOF
```



•10、修改 limits.conf 文件

修改 /etc/security/limits.conf 文件：

```shell
sed -i '/*            soft    fsize/d' /etc/security/limits.conf
sed -i '/*            hard    fsize/d' /etc/security/limits.conf
sed -i '/*            soft    cpu/d' /etc/security/limits.conf
sed -i '/*            hard    cpu/d' /etc/security/limits.conf
sed -i '/*            soft    as/d' /etc/security/limits.conf
sed -i '/*            hard    as/d' /etc/security/limits.conf
sed -i '/*            soft    nofile/d' /etc/security/limits.conf
sed -i '/*            hard    nofile/d' /etc/security/limits.conf
sed -i '/*            soft    nproc/d' /etc/security/limits.conf
sed -i '/*            hard    nproc/d' /etc/security/limits.conf
cat << eof="">> /etc/security/limits.conf
*            soft    fsize           unlimited
*            hard    fsize           unlimited
*            soft    cpu             unlimited
*            hard    cpu             unlimited
*            soft    as              unlimited
*            hard    as              unlimited
*            soft    nofile          1024000
*            hard    nofile          1024000
*            soft    nproc           1024000
*            hard    nproc           1024000
EOF
```



•11、修改 20-nproc.conf 文件

修改 /etc/security/limits.d/20-nproc.conf 文件内容如下：

```shell
cat << eof=""> /etc/security/limits.d/20-nproc.conf
# Default limit for number of user's processes to prevent
# accidental fork bombs.
# See rhbz #432903 for reasoning.
*          soft    nproc     1024000
root       soft    nproc     unlimited
EOF
```



•12、配置主机名

•13、配置主机名与 IP 映射关系

•14、配置 ntp 时间同步

•15、关闭 THP