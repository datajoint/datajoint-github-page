---
title: MySQL Server Configuration
permalink: dj_mysql_config.html
sidebar: dj_sidebar
tags: [special_layouts]
keywords: frequently asked questions, FAQ, question and answer
last_updated: November 30, 2015
summary: ""

folder: dj/references
---

### Have a database administrator?
If your system administrator is kind enough to set up and administer a MySQL server for you, kindly request an account, a user name, and schemas to host your project.

### Or set up your own MySQL server
You can also set up a server on your own.  There are multiple resources online describing the installation process in detail and I will not attempt to reproduce them here. The easiest way is to use an installation package that installs the entire bundle of software required to run a MySQL server.  For example, the [MAMP](http://www.mamp.info) package for MacOS X installs Apache server, MySQL server, and PHP as a stand-alone application and immediately launches the server.
#### A basic MySQL server configuration file
Upon installation, you will need to update the server configuration file (usually `my.cnf` or `my.ini`)  to accommodate large data packages that you will be sending and receiving from the server. Here is a minimal configuration file:

```bash
[mysqld]
max_allowed_packet=512M

innodb_log_file_size=2G
innodb_buffer_pool_size=4G
innodb_log_buffer_size=8M
innodb_file_per_table
innodb_stats_on_metadata=0

log-bin=mysqllog
```
* `max_allowed_packet` is adjusted to allow for the storage of tuples with large blobs of Matlab data. Increase further if you expect to store even more than 512 MiB in a tuple.
* `innodb_log_file_size` is set to a small multiple of `max_allowed_packet` as InnoDB expects several rows to fit into its log. If this value is set too small, errors will appear in the MySQL log upon insertion of large tuples.
* `innodb_buffer_pool_size` determines how much RAM the InnoDB storage engine can use to cache data. Data in the cache can be accessed more rapidly than data on disk. On dedicated MySQL servers you can set this value to a significant fraction of your total RAM. Otherwise, set the value based on available RAM and the needs of other processes on the server
* `innodb_log_buffer_size` is increased slightly to reduce the number of disk I/O operations when data is inserted into tables
* `innodb_file_per_table` splits the tablespace across multiple files. This allows disk space to be reclaimed after data is deleted from tables.
* `innodb_stats_on_metadata=0` accelerates queries of MySQL's `INFORMATION_SCHEMA` tables. These are used by DataJoint to discover dependencies between tables.
* `log-bin=mysqllog`: Binary logging is enabled because it facilitates efficient incremental backups of the database. Change the filename after the equal sign to your liking.

#### An advanced configuration example
Here is a more complex configuration file with lots of tuning parameters taken from a production server installed using MAMP.

```bash
# Example MySQL config file for very large systems.
#
# This is for a large system with memory of 1G-2G where the system runs mainly
# MySQL.
#
# MySQL programs look for option files in a set of
# locations which depend on the deployment platform.
# You can copy this option file to one of those
# locations. For information about these locations, see:
# http://dev.mysql.com/doc/mysql/en/option-files.html
#
# In this file, you can use all long options that a program supports.
# If you want to know which options a program supports, run the program
# with the "--help" option.

# The following options will be passed to all MySQL clients
[client]
#password	= your_password
port		= 3306
#socket		= /tmp/mysql.sock
default-character-set=latin1

# Here follows entries for some specific programs

# The MySQL server
[mysqld]
port		= 3306
#socket		= /tmp/mysql.sock
skip-external-locking
key_buffer_size = 384M
max_allowed_packet = 1M
table_open_cache = 512
sort_buffer_size = 2M
read_buffer_size = 2M
read_rnd_buffer_size = 8M
myisam_sort_buffer_size = 64M
thread_cache_size = 8
query_cache_size = 32M
# Try number of CPU's*2 for thread_concurrency
thread_concurrency = 8

# copied from at-storage
default-storage-engine=INNODB
sql-mode="TRADITIONAL,STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION"
innodb_file_per_table
max_connections=1000
query_cache_size=512M
query_cache_limit=32M
table_cache=2048
tmp_table_size=205M
thread_cache_size=8
innodb_additional_mem_pool_size=200M
innodb_flush_log_at_trx_commit=1
innodb_buffer_pool_size=3000M
innodb_read_io_threads=16
innodb_write_io_threads=16
innodb_max_dirty_pages_pct=90
innodb_thread_concurrency=10
innodb_stats_on_metadata=0
max_allowed_packet=1024M
interactive_timeout=12600
wait_timeout=20600
max_user_connections=100
#skip-innodb_fast_shutdown


# Don't listen on a TCP/IP port at all. This can be a security enhancement,
# if all processes that need to connect to mysqld run on the same host.
# All interaction with mysqld must be made via Unix sockets or named pipes.
# Note that using this option without enabling named pipes on Windows
# (via the "enable-named-pipe" option) will render mysqld useless!
#
#skip-networking

# Replication Master Server (default)
# binary logging is required for replication
log-bin=mysql-bin

# required unique id between 1 and 2^32 - 1
# defaults to 1 if master-host is not set
# but will not function as a master if omitted
server-id	= 1

[mysqldump]
quick
max_allowed_packet = 16M

[mysql]
no-auto-rehash
# Remove the next comment character if you are not familiar with SQL
#safe-updates

[myisamchk]
key_buffer_size = 256M
sort_buffer_size = 256M
read_buffer = 2M
write_buffer = 2M

[mysqlhotcopy]
interactive-timeout

[mysqld_safe]
open-files-limit=8192
```
