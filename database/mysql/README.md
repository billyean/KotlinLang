# Work on code

[Official Link for Source Code](https://dev.mysql.com/doc/internals/en/guided-tour.html)
Caveat: MySQL build depends on boost 1.59.0. You can easily download source and do
sh bootstrap.sh && ./b2 && ./b2 install
If you have install other version of boost, you can uninstall it with
rm /usr/local/lib/libboost*
rm -rf /usr/local/include/boost
