# Reading

[MySQL Internals Manual](https://dev.mysql.com/doc/internals/en/)
[MySQL Source Code Documentation](https://dev.mysql.com/doc/dev/mysql-server/8.0.0/)

My reading is based on mysql server version 8.0.0.

# Work on code

* [Official Link for Source Code](https://dev.mysql.com/doc/internals/en/guided-tour.html)

Caveat: MySQL build depends on boost 1.59.0. You can easily download source and do
```shell
# sh bootstrap.sh && ./b2 && ./b2 install
```

If you have install other version of boost, you can uninstall it with
```shell
# rm /usr/local/lib/libboost*
# rm -rf /usr/local/include/boost
```

* [Source](https://github.com/mysql/mysql-server.git)
* [How to build](https://dev.mysql.com/doc/refman/8.0/en/installing-source-distribution.html)

Two tweak have been done
```shell
Since I was building in Ubuntu, I use sudo prepend all command.
# cmake need a boost dir, which you can provide a download option
# cmake .. -DDOWNLOAD_BOOST=1 -DWITH_BOOST=~/code/boost/
```


