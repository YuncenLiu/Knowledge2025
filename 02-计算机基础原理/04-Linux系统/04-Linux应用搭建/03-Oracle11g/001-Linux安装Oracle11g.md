---
createTime: 2025-04-14
host: HomeVM_root@192.168.126.8
---
来源 CSDN：[Linux安装 Oracle11g](https://blog.csdn.net/qq_43278189/article/details/123687994)
安装包：`我的网盘>002-开发工具>001-数据库>001-Oracle-001-oracle11g Linux`

## 1、基础配置

1、修改主机名
```sh
vim /etc/hosts
192.168.126.8   oracle

hostnamectl set-hostname oracle
```

2、上传文件到服务器 /opt/oracle 目录下
```sh
linux.x64_11gR2_database_1of2.zip
linux.x64_11gR2_database_2of2.zip
```
获得 database 文件夹，将文件夹挪到 /software/ 目录下
```sh
mkdir /software
mv database/ /software/
```

3、关闭 selinux & 关闭防火墙

参考：[003-搭建服务器-CentOS7.9](../../01-VMWare搭建/003-搭建服务器-CentOS7.9.md)

4、下载依赖：
```sh
yum install gcc make binutils gcc-c++ compat-libstdc++-33elfutils-libelf-devel elfutils-libelf-devel-static ksh libaio libaio-develnumactl-devel sysstat unixODBC unixODBC-devel pcre-devel –y
```

## 2、添加用户组

```sh
groupadd oinstall
groupadd dba
useradd -g oinstall -G dba oracle
passwd oracle
# 密码也是 oracle

# 查看 oracle 用户
id oracle
uid=1002(oracle) gid=1002(oinstall) groups=1002(oinstall),1003(dba)
```

修改内核参数 `vim /etc/sysctl.conf`

> [!Note]
> 其中kernel.shmmax = 6442450944  = 6G 为本机物理内存（15G）的一半，单位为byte。

```c
# 这个参数是用来设置系统能够支持的最大异步 I/O 请求数。Oracle 需要较高的异步 I/O 数量
fs.aio-max-nr = 1048576
# 这个参数控制了系统可以打开的最大文件数
fs.file-max = 6815744
# 控制了共享内存的总量（单位为页面）
kernel.shmall = 2097152
# 控制单个共享内存段的最大大小
kernel.shmmax = 6442450944
# 控制系统中共享内存段的最大数量
kernel.shmmni = 4096
# 这个参数定义了信号量设置
kernel.sem = 250 32000 100 128
# 这个参数控制了本地端口的范围
net.ipv4.ip_local_port_range = 9000 65500
# 这些参数控制了接收缓冲区的大小
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
# 控制发送缓冲区的大小
net.core.wmem_default = 262144
net.core.wmem_max = 1048576
```

激活 `sysctl -p`

修改用户的限制文件 `vim /etc/security/limits.conf`

```
oracle           soft    nproc           2047
oracle           hard    nproc           16384
oracle           soft    nofile          1024
oracle           hard    nofile         65536
oracle           soft    stack           10240
```

修改 `/etc/pam.d/login`
```sh
 session     required    /lib64/security/pam_limits.so
 session     required    pam_limits.so
```

修改 `/etc/profile`
```
#oracle配置
if [ $USER = "oracle" ]; then
  if [ $SHELL = "/bin/ksh" ]; then
      ulimit -p 16384
      ulimit -n 65536
  else
      ulimit -u 16384 -n 65536
  fi
fi
```

激活配置 `source /etc/profile`

## 3、创建安装目录

```sh
mkdir -p /data/oracle/product/11.2.0
mkdir /data/oracle/oradata
mkdir /data/oracle/inventory
mkdir /data/oracle/fast_recovery_area
chown -R oracle:oinstall /data/oracle
chmod -R 775 /data/oracle

# oracle 安装包
chown -R oracle:oinstall /software
```

切换到 oracle 用户下

```sh
su -l oracle
```

编辑配置文件 `vim ~/.bash_profile`

```
# ORACLE 配置 2025-04-15
ORACLE_BASE=/data/oracle
ORACLE_HOME=$ORACLE_BASE/product/11.2.0
ORACLE_SID=orcl
PATH=$PATH:$ORACLE_HOME/bin
export ORACLE_BASE ORACLE_HOME ORACLE_SID PATH
```

激活配置 `source ~/.bash_profile`


## 4、编辑静默安装响应文件

复制安装文件夹 response 到当前 oracle 用户家目录
```sh
# 当前在 /home/oracle

cp -R /software/database/response/ .
```
修改 `vim response/db_install.rsp`

需要修改的内容如下
```sh
oracle.install.option=INSTALL_DB_SWONLY
# 设置的系统主机名
ORACLE_HOSTNAME=oracle
# 指定要为库存目录设置的Unix组。
UNIX_GROUP_NAME=oinstall
# 指定存放库存文件的位置。
INVENTORY_LOCATION=/data/oracle/inventory
# 指定将要安装组件的语言。
SELECTED_LANGUAGES=en,zh_CN
# 指定Oracle主目录的完整路径。
ORACLE_HOME=/data/oracle/product/11.2.0
# 指定Oracle Base的完整路径。
ORACLE_BASE=/data/oracle
# 指定组件的安装版本。 EE: 企业版
oracle.install.db.InstallEdition=EE
# DBA_GROUP是要授予OSDBA权限的操作系统组。
oracle.install.db.DBA_GROUP=dba
# OPER_GROUP是要授予操作系统权限的操作系统组。
oracle.install.db.OPER_GROUP=dba
# 指定用户是否要提供连接的任何代理详细信息
DECLINE_SECURITY_UPDATES=true
```

## 5、根据文件静默安装 Oracle11g

> [!Note]
> 注意，使用 oracle 用户操作

```sh
cd /software/database/

./runInstaller -silent -responseFile /home/oracle/response/db_install.rsp -ignorePrereq
```

### 5.1、安装时避免出现交换空间不足（可以不执行）

root 用户下执行
```sh
dd if=/dev/zero of=swapfile bs=1024 count=500000
mkswap swapfile
swapon swapfile
# 最后验证，有 500M 的SWAP空间
free -m
```

## 6、当执行完安装命令后

使用root用户
```sh
du -sh /data
```
可以发现 data 目录在不断变大，说明正在安装。

![](images/Pasted%20image%2020250415212841.png)

大概1分钟不到，就安装好了。
使用 root 用户执行这两个脚本

```sh
sh /data/oracle/inventory/orainstRoot.sh
sh /data/oracle/product/11.2.0/root.sh
```

## 7、静默方式监听

切回 oracle 用户
```sh
su -l oracle
```

```sh
netca /silent /responseFile /home/oracle/response/netca.rsp
```
![](images/Pasted%20image%2020250415213221.png)

注意此处，必须使用/silent /responseFile格式，而不是-silent -responseFile，因为是静默安装。
此时，/data/oracle/product/11.2.0/network/admin 会有

![](images/Pasted%20image%2020250415213355.png)

## 8、开启&关闭 Oracle

```sh
#开启监听
lsnrctl start    
#查看监听状态
lsnrctl status
#关闭监听
lsnrctl status
```

此时服务已经启动，查看端口 

```
ss -anltp|grep 1521
```

## 9、以静默方式建立新库，同时也建立一个对应的实例

oracle 用户操作
```sh
vim /home/oracle/response/dbca.rsp
```

```sh
# 全局数据库的名字=SID+主机域名
GDBNAME = "orcl.oracle"

# 实例名称（对应上面的 实例名称） （重要）
SID = "orcl"

# SYS 管理员密码
SYSPASSWORD = "123456"

# SYSTEM 管理员密码
SYSTEMPASSWORD = "123456"

# SYSMAN 管理员密码
SYSMANPASSWORD = "123456"

# DBSNMP 管理员密码
DBSNMPPASSWORD = "123456"

# 数据文件存放位置
DATAFILEDESTINATION =/data/oracle/oradata
# 恢复数据存放目录
RECOVERYAREADESTINATION=/data/oracle/fast_recovery_area

# 字符集，重要!!! 建库后一般不能更改，所以建库前要确定清楚。
CHARACTERSET = "AL32UTF8"

# oracle内存6554MB,物理内存8G*80%
TOTALMEMORY = "6554"
```

进行静默配置
```sh
dbca -silent -responseFile /home/oracle/response/dbca.rsp
```

建库后实例检查
```sh
ps -ef | grep ora_ | grep -v grep
```

监听状态
```sh
lsnrctl status
```

![](images/Pasted%20image%2020250415220555.png)

数据库创建完成。
有关详细信息, 请查看以下位置的日志文件: /data/oracle/cfgtoollogs/dbca/orcl/orcl.log。
数据库信息:
全局数据库名:orcl.oracle
系统标识符 (SID):orcl


## 10、登录实例

```sh
sqlplus / as sysdba

> select status from v$instance;
```



## 11、优雅的停机及启动

停机
1、关闭服务器
```sh
su -l oracle
sqlplus / as sysdba

# 该选项会立即关闭数据库，正在进行的事务会被回滚。用户连接会被断开，但会确保不丢失数据。
> SHUTDOWN IMMEDIATE;
# 强制退出
> SHUTDOWN ABORT;
```
2、关闭监听
```sh
lsnrctl stop
```

完全关闭状态：此时 1521 端口彻底关闭
![](images/Pasted%20image%2020250415221632.png)


启动
1、启动服务器
```sh
su -l oracle
sqlplus / as sysdba

# 启动
> STARTUP;
# 查询状态
> SELECT status FROM v$instance;
```
![](images/Pasted%20image%2020250415221803.png)

2、启动监听
```sh
lsnrctl start
```

![](images/Pasted%20image%2020250415221922.png)

出现此表示 Oracle 启动成功！