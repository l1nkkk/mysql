- [配置文件](#%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6)
- [存储引擎](#%E5%AD%98%E5%82%A8%E5%BC%95%E6%93%8E)
  - [有关的一些命令和操作](#%E6%9C%89%E5%85%B3%E7%9A%84%E4%B8%80%E4%BA%9B%E5%91%BD%E4%BB%A4%E5%92%8C%E6%93%8D%E4%BD%9C)
- [未分类常用命令](#%E6%9C%AA%E5%88%86%E7%B1%BB%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4)
- [SQL的语法规范](#SQL%E7%9A%84%E8%AF%AD%E6%B3%95%E8%A7%84%E8%8C%83)
- [数据库（database）](#%E6%95%B0%E6%8D%AE%E5%BA%93database)
  - [默认数据库](#%E9%BB%98%E8%AE%A4%E6%95%B0%E6%8D%AE%E5%BA%93)

- 启动mysql:
mysql -h host -P mysqlport -u user -p[直接密码不用空格] 
# 配置文件
如果是win，是my.ini。如果是linux是my.cnf。更改之后需要重启mysql  
mysql version如下
```
l1nkkk@l1nkkk-TM1701:~$ mysql --version
mysql  Ver 14.14 Distrib 5.7.29, for Linux (x86_64) using  EditLine wrapper
```
在ubuntu中配置文件在`/etc/mysql`里。可以在` /etc/mysql/mysql.conf.d/mysqld.cnf`查看mysql服务的配置。以下是mysqld.conf的内容。
> mysql配置文件和Innodb各参数解析：https://www.cnblogs.com/cheng2015/p/7685017.html
```sh
#
# The MySQL database server configuration file.
#
# You can copy this to one of:
# - "/etc/mysql/my.cnf" to set global options,
# - "~/.my.cnf" to set user-specific options.
# 
# One can use all long options that the program supports.
# Run program with --help to get a list of available options and with
# --print-defaults to see which it would actually understand and use.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

# This will be passed to all mysql clients
# It has been reported that passwords should be enclosed with ticks/quotes
# escpecially if they contain "#" chars...
# Remember to edit /etc/mysql/debian.cnf when changing the socket location.

# Here is entries for some specific programs
# The following values assume you have at least 32M ram

[mysqld_safe]
socket		= /var/run/mysqld/mysqld.sock
nice		= 0

[mysqld]
#
# * Basic Settings
#
user		= mysql
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
port		= 3306
basedir		= /usr
datadir		= /var/lib/mysql
tmpdir		= /tmp
lc-messages-dir	= /usr/share/mysql
skip-external-locking
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address		= 127.0.0.1
#
# * Fine Tuning
#
key_buffer_size		= 16M
max_allowed_packet	= 16M
thread_stack		= 192K
thread_cache_size       = 8
# This replaces the startup script and checks MyISAM tables if needed
# the first time they are touched
myisam-recover-options  = BACKUP
#max_connections        = 100
#table_open_cache       = 64
#thread_concurrency     = 10
#
# * Query Cache Configuration
#
query_cache_limit	= 1M
query_cache_size        = 16M
#
# * Logging and Replication
#
# Both location gets rotated by the cronjob.
# Be aware that this log type is a performance killer.
# As of 5.1 you can enable the log at runtime!
#general_log_file        = /var/log/mysql/mysql.log
#general_log             = 1
#
# Error log - should be very few entries.
#
log_error = /var/log/mysql/error.log
#
# Here you can see queries with especially long duration
#slow_query_log		= 1
#slow_query_log_file	= /var/log/mysql/mysql-slow.log
#long_query_time = 2
#log-queries-not-using-indexes
#
# The following can be used as easy to replay backup logs or for replication.
# note: if you are setting up a replication slave, see README.Debian about
#       other settings you may need to change.
#server-id		= 1
#log_bin			= /var/log/mysql/mysql-bin.log
expire_logs_days	= 10
max_binlog_size   = 100M
#binlog_do_db		= include_database_name
#binlog_ignore_db	= include_database_name
#
# * InnoDB
#
# InnoDB is enabled by default with a 10MB datafile in /var/lib/mysql/.
# Read the manual for more InnoDB related options. There are many!
#
# * Security Features
#
# Read the manual, too, if you want chroot!
# chroot = /var/lib/mysql/
#
# For generating SSL certificates I recommend the OpenSSL GUI "tinyca".
#
# ssl-ca=/etc/mysql/cacert.pem
# ssl-cert=/etc/mysql/server-cert.pem
# ssl-key=/etc/mysql/server-key.pem
```

# 存储引擎
## 有关的一些命令和操作

- mysql现在已提供什么存储引擎（DEFAULT表示现在默认所选用）:  d
`mysql> show engines;`
 
- ysql当前默认的存储引擎:  
`mysql> show variables like '%storage_engine%';`
 
- 你要看某个表用了什么引擎(在显示结果里参数engine后面的就表示该表当前用的存储引擎):    
`mysql> show create table 表名;`  

- 查看mysql版本：  
`select version();`

- 设置默认存储引擎：  
`在配置文件my.cnf中的 [mysqld] 下面加入
default-storage-engine=INNODB 一句`

- 重启mysql服务器：  
`service mysqld restart`或  
`mysqladmin -u root -p shutdown`

- 查看表使用的存储引擎：  
  - 两种方法：  
   a.` show table status from db_name where name='table_name';`  
   b. `show create table table_name;`
如果显示的格式不好看，可以用\g代替行尾分号

- 修改表引擎方法：  
`alter table table_name engine=innodb;`

- 关闭Innodb引擎方法  
关闭mysql服务：` net stop mysql`  
找到mysql安装目录下的my.ini文件：  
找到`default-storage-engine=INNODB` 改为`default-storage-engine=MYISAM`  
找到#skip-innodb 改为`skip-innodb`  
启动mysql服务：`net start mysql`  

# 未分类常用命令
| 功能       | 命令                   |
| ---------- | ---------------------- |
| 显示数据库 | show databases;        |
| 显示数据表 | show tables [from db]; |
| 当前所在库 | select databse         |
| 显示表的结构| desc table_name |
| 显示版本 | select version() |

# SQL的语法规范

# 数据库（database）
- 显示所有数据库：  
  `show databases;`

## 默认数据库

**默认数据库分类：**

- information_schema
- performance_schema
- mysql
- test

**数据库信息：informance_schema**

- 保存了MySQl服务所有**数据库的信息**。
- 具体MySQL服务有多少个数据库，各个数据库有哪些表，各个表中的字段是什么数据类型，各个表中有哪些索引，各个数据库要什么权限才能访问。  
**权限，管理，部署：mysql**

- 保存MySQL的权限、参数、对象和状态信息。
- 如哪些user可以访问这个数据、DB参数、插件、主从

**性能：performance_schema**

- 主要用于收集数据库服务器性能参数
提供进程等待的详细信息，包括锁、互斥变量、文件信息；
保存历史的事件汇总信息，为提供MySQL服务器性能做出详细的判断；
对于新增和删除监控事件点都非常容易，并可以随意改变mysql服务器的监控周期，例如（CYCLE、MICROSECOND）

**test**  
测试用，没有东西