# inode

> The inode is a data structure in a Unix-style file system that describes a filesystem object such as a file or a 
> directory. Each inode stores the attributes and disk block location(s) of the object's data. Filesystem object 
> attributes may include metadata (times of last change,access, modification), as well as owner and permission data.
  
>  Directories are lists of names assigned to inodes. A directory contains an entry for itself, its parent, and each of
> its children.

[struct inode](https://github.com/torvalds/linux/blob/master/include/linux/fs.h)

|member             |Description    |
|-------------------|---------------|
|i_ino              |subscript in inode array|
|imode              |mode info|
|i_uid              |owner id|
|i_gid              |owner's group id|
|i_count            |My guess is it's the number of files that is using this inode, possibly related to hard link, plus 1 when create a hard link on the same inode|
|i_writecount       |My guess is write process number|
|i_data             |data info. for directory it contains|

|method             |Description    |
|-------------------|---------------|
|||



[struct super_block](https://github.com/torvalds/linux/blob/master/include/linux/fs.h)