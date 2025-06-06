---
createTime: 2025-06-06
---
适用于人大金仓数据库，因为他基于 pg 数据库

## 准备工作

### 服务器硬件环境

CentOS6.5以上
```sh
cat /proc/version
cat /etc/redhat-release
```

建议使用 XFS 文件系统
```sh
which xfs_quota
```

64位 CPU
```sh
cat /proc/cpuinfo
```

服务器最小2G内存
```sh
free -g
```

### 数据库环境

源 Oracle 数据库信息：
```sh
ORACLE_DSN         dbi:Oracle:host=dcpdb.prd.klhic.com;sid=BIDB;port=1522
ORACLE_USER        DATAMART
ORACLE_PWD         DMBIDB105254
```

目标 Pg 数据库信息
```sh
10.6.74.34:55432
mppadmin/kingbase@kadb
database:ods
```


## ora2pg 预安装

下载资源如下：百度网盘/002-开发工具/006-数据库同步/02-ora2pg

![](images/Pasted%20image%2020250606173506.png)

## 安装 DBI

查看 perl 版本, 保证 5.15 以上
```sh
perl -v
```

解压 DBI
```sh
tar -xvf  DBI-1.637.tar.gz
cd DBI-1.637
```

验证 perl 是否包含 `ExtUtils::MakeMaker` 模块
```sh
perl -MExtUtils::MakeMaker -e 1

# 如果没有则执行
yum install perl-ExtUtils-MakeMaker -y
```

编译
```sh
perl Makefile.PL
make
make test

# 因为我使用非 root 权限安装，安装过程会在 /usr/local 下生成文件
sudo make install
```


## 安装 Oracle 客户端

参考 CSDN：[https://blog.csdn.net/w345731923/article/details/140102573](https://blog.csdn.net/w345731923/article/details/140102573)

创建 oracle 用户

```sh
useradd oracle
unzip instantclient-basic-linux.x64-11.2.0.4.0.zip -d /home/oracle
unzip instantclient-sqlplus-linux.x64-11.2.0.4.0.zip -d /home/oracle
unzip instantclient-jdbc-linux.x64-11.2.0.4.0.zip -d /home/oracle
unzip instantclient-sdk-linux.x64-11.2.0.4.0.zip -d /home/oracle
```

文件此时都解压进了 /home/oracle/instantclient_11_2 目录
编写系统环境变量（使用Root）

```sh
vi /etc/profile

# OracleClient 2025-06-05
ORACLE_BASE=/home/oracle/instantclient_11_2
ORACLE_HOME=/home/oracle/instantclient_11_2
LD_LIBRARY_PATH=$ORACLE_HOME:$LD_LIBRARY_PATH
PATH=$PATH:$ORACLE_HOME
export ORACLE_BASE ORACLE_HOME PATH LD_LIBRARY_PATH
```

创建 ora 文件
```sh
mkdir -p /home/oracle/instantclient_11_2/network/admin
cd /home/oracle/instantclient_11_2/network/admin
```

编写 ora 文件
```sh
vi tnsnames.ora

ORCL =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = dcpdb.prd.klhic.com)(PORT = 1522))
    )
    (CONNECT_DATA =
      (SERVICE_NAME = BIDB)
    )
  )
```

刷新生效
```sh
source /etc/profile
```

验证是否生效
```sh
echo $ORACLE_BASE
echo $ORACLE_HOME
echo $LD_LIBRARY_PATH
```

## 安装 DBD::Oracle

因为这里需要使用 oracle 客户端，所以操作都是用 oracle 用户操作
```sh
tar -xvf DBD-Oracle-1.74.tar.gz
cd DBD-Oracle-1.74
perl Makefile.PL

make
make test（可选）
sudo make install
```

编写脚本 vi list.pl

```
#!/usr/bin/perl
use strict;
use ExtUtils::Installed;
my $inst= ExtUtils::Installed->new();
my @modules = $inst->modules();
foreach(@modules)
{
        my $ver = $inst->version($_) || "???";
        printf("%-12s --  %s\n", $_, $ver);  
}
exit;
```

编译 DBD
```sh
perl list.pl

# 输出：
# DBD::Oracle  --  1.74
# DBI          --  1.637
# Perl         --  5.16.3
```

## 安装 ora2pg

```sh
tar -xvf ora2pg-21.1.tar.bz2
cd ora2pg-21.1
perl Makefile.PL
sudo make && sudo make install
```

编译 DBD
```sh
perl list.pl

# DBD::Oracle  --  1.74
# DBI          --  1.637
# Ora2Pg       --  21.1
# Perl         --  5.16.3
```

![](images/Pasted%20image%2020250606174324.png)

此时可以成功使用 ` ora2pg -v` 命令，安装完成


## 同步配置

### 生成表结构

```conf
PG_VERSION         9.4
ORACLE_HOME        /home/oracle/instantclient_11_2
ORACLE_DSN         dbi:Oracle:host=dcpdb.prd.klhic.com;sid=BIDB;port=1522
ORACLE_USER        DATAMART
ORACLE_PWD         DMBIDB105254

TYPE               TABLE
SCHEMA             ODS

OUTPUT_DIR         /data/apps/ora2pg/oracle_mate
OUTPUT             O_LIS_LCCONT.sql

ALLOW              O_LIS_LCCONT
```


### 生成数据
```conf
PG_VERSION         9.4
ORACLE_HOME        /home/oracle/instantclient_11_2
ORACLE_DSN         dbi:Oracle:host=dcpdb.prd.klhic.com;sid=BIDB;port=1522
ORACLE_USER        DATAMART
ORACLE_PWD         DMBIDB105254

TYPE               COPY
SCHEMA             ODS
ALLOW              O_LIS_LCCONT
FORMAT             binary

JOBS              32
BUFFER_SIZE       256MB
FETCH_SIZE        10000
BLOB_LIMIT        10MB
MEMORY_LIMIT      48GB
PARALLEL_TABLES   8
CHILD_PROCESSES   4

ESCAPE_BACKSLASH  1
STANDARD_CONFORMING_STRINGS 1
NULL_STRING       'NULL'
COPY_DELIMITER    'PG_EOF' 
REPLACE_CRLF       1       
TRIM_FIELDS       1        
FORCE_TYPE        insuredsex:varchar(1)

DATA_CHECK        insuredsex:~'^[A-Za-z0-9]$'
INVALID_CHAR_REPLACE 1
DISABLE_TRIGGERS 1
SKIP             fkeys indexes checks

OUTPUT_DIR        /data/apps/ora2pg/oracle_data
OUTPUT            O_LIS_LCCONT_6.sql
NO_HEADER         1
COMPILE_SCHEMA    0
```


## 如何执行

```sh
ora2pg -c ora2pg.conf
```