# File

* [sql/mysqld.h](https://github.com/mysql/mysql-server/blob/5.7/sql/mysqld.h)
* [sql/mysqld.cc](https://github.com/mysql/mysql-server/blob/5.7/sql/mysqld.cc)

# function

## Startup

* mysqld_main(other platforms)/win_main(windows)

* init_common_variables - initialize all global variables

> * Default storage engine is InnoDB in most of platform but it's MyISAM in embedded system.
> * init_thread_environment
> * mysql_init_variables initialize MySQL global variables to default values.

## Shutdown

* void handle_fatal_signal(int sig)	
* void * signal_hand(void *arg)	


