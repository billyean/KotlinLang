# Work on code

[Official Link for Source Code](https://dev.mysql.com/doc/internals/en/guided-tour.html)
Caveat: MySQL build depends on boost 1.59.0. You can easily download source and do
sh bootstrap.sh && ./b2 && ./b2 install
If you have install other version of boost, you can uninstall it with
rm /usr/local/lib/libboost*
rm -rf /usr/local/include/boost

[Source](https://github.com/mysql/mysql-server.git)
[How to build](https://dev.mysql.com/doc/mysql-sourcebuild-excerpt/5.7/en/installing-source-distribution.html)
Two tweak have been done
* Since I was building in Ubuntu, I use sudo prepend all command.
* cmake need a boost dir, which you can provide a download option
  cmake .. -DDOWNLOAD_BOOST=1 -DWITH_BOOST=~/code/boost/
