# inode

> The inode is a data structure in a Unix-style file system that describes a filesystem object such as a file or a 
> directory. Each inode stores the attributes and disk block location(s) of the object's data. Filesystem object 
> attributes may include metadata (times of last change,access, modification), as well as owner and permission data.
  
>  Directories are lists of names assigned to inodes. A directory contains an entry for itself, its parent, and each of
> its children.

[struct inode](https://github.com/torvalds/linux/blob/master/include/linux/fs.h)

|member                |Description    |
|----------------------|---------------|
|i_ino                 |`subscript in inode array`|
|imode                 |`mode info`|
|i_uid                 |`owner id`|
|i_gid                 |`owner's group id`|
|i_count               |My guess is it's the number of files that is using this inode, possibly related to hard link, plus 1 when create a hard link on the same inode|
|i_writecount          |My guess is write process number|
|i_data                |`data info. for directory it contains`|

[inode.c](https://github.com/torvalds/linux/blob/master/fs/inode.c)

|method                |Description    |
|----------------------|---------------|
|get_nr_inodes         |`iterator every cpus to get total number of inodes`|
|get_nr_inodes_unused  |`iterator every cpus to get total number of unused inodes`|
|ilookup               ||

# superblock

[struct super_block](https://github.com/torvalds/linux/blob/master/include/linux/fs.h)

> superblock gives global information on a filesystem: the device on which it lives, its block size, its type, the 
> dentry of the root of the filesystem, the methods it has

|member                |Description    |
|----------------------|---------------|
|s_type                |`file system type`|
|s_dev                 |`search index for device`|
|s_root                |`dentry of this super block`|

# Operations

[struct inode_operations](https://github.com/torvalds/linux/blob/master/include/linux/fs.h)

|member                |Description    |
|----------------------|---------------|
|lookup                |`look a dentry under a inode`|
|get_link              ||
|permission            ||
|get_acl               ||
|readlink              ||
|create                ||
|link                  ||
|unlink                ||
|symlink               ||
|mkdir                 ||
|rmdir                 ||
|mknod                 ||
|rename                ||
|getattr               ||
|listxattr             ||
|fiemap                ||
|update_time           ||
|atomic_open           ||
|tmpfile               ||
|set_acl               ||

[struct super_operations](https://github.com/torvalds/linux/blob/master/include/linux/fs.h)

|member                |Description    |
|----------------------|---------------|
|alloc_inode           ||
|destroy_inode         ||
|dirty_inode           ||
|write_inode           ||
|drop_inode            ||
|evict_inode          ||
|put_super            ||

[struct file_operations](https://github.com/torvalds/linux/blob/master/include/linux/fs.h)

# Path look up process




