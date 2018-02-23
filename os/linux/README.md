# Build
[Ubuntu Build for Kernel](https://wiki.ubuntu.com/KernelTeam/GitKernelBuild)

# Module

## Virtual File System
### inode
[struct inode](https://github.com/torvalds/linux/blob/master/include/linux/fs.h)
* i_ino - subscript in inode array
* imode - mod info
* i_uid - owner id
* i_gid - owner group id
* i_blocks - block number
* i_count - [My guess is it's the number of files that is using this inode, possibly related to hard link, plus 1 when create a hard link on the same inode]
* i_writecount - [My guess is write process number]
* i_data - data info. for directory it contains 

[struct super_block](https://github.com/torvalds/linux/blob/master/include/linux/fs.h)

#### Command
* tune2fs -l [device] - display file system info 
* df -i - inode usage rate
* ls -ai - list all files and their inodes under current folder


# Reference
## [CLion INotify Watch is too low warning](https://confluence.jetbrains.com/display/IDEADEV/Inotify+Watches+Limit) 

in Ubuntu, just do

'''
sudo sysctl fs.inotify.max_user_watches=xxxxx
'''