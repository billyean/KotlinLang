# Build
[Ubuntu Build for Kernel](https://wiki.ubuntu.com/KernelTeam/GitKernelBuild)

# Module

* [Virtual File System](vfs/)
* [Memory Management]()

#### Process to look up a file

#### Command
* tune2fs -l [device] - display file system info 
* df -i - inode usage rate
* ls -ai - list all files and their inodes under current folder


# Reference
## [CLion INotify Watch is too low warning](https://confluence.jetbrains.com/display/IDEADEV/Inotify+Watches+Limit) 

in Ubuntu, just do

```shell
sudo sysctl fs.inotify.max_user_watches=xxxxx
```