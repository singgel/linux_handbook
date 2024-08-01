#查看开机信息
dmesg
#查看文件版本
md5sum

-------------
$ iotop -oP
#命令的含义：只显示有I/O行为的进程
$ iostat -dtxNm 2 10
#查看磁盘io状况
$ dstat -r -l -t --top-io
#用dstat命令看下io前几名的进程
$ dstat --top-bio-adv
#找到那个进程占用IO最多
$ pidstat -d 1
#命令的含义：展示I/O统计，每秒更新一次

## linux查看哪个进程占用磁盘IO  
$ vmstat 2  
执行vmstat命令，可以看到r值和b值较高，r 表示运行和等待cpu时间片的进程数，如果长期大于1，说明cpu不足，需要增加cpu。  
b 表示在等待资源的进程数，比如正在等待I/O、或者内存交换等。

--------------
### [IO等待导致性能下降](https://serverfault.com/questions/363355/io-wait-causing-so-much-slowdown-ext4-jdb2-at-99-io-during-mysql-commit)
$ iotop -oP  
命令的含义：只显示有I/O行为的进程  

$ iostat -dtxNm 2 10  
查看磁盘io状况

$ dstat -r -l -t --top-io  
用dstat命令看下io前几名的进程

$ dstat --top-bio-adv  
找到那个进程占用IO最多

$ pidstat -d 1  
命令的含义：展示I/O统计，每秒更新一次  

### [Linux 挂载管理(mount)](https://www.cnblogs.com/chenmh/p/5097530.html)
$ vim /etc/fstab  
mount挂载分区在系统重启之后需要重新挂载，修改/etc/fstab文件可使挂载永久生效

$ mount -t ext4 /dev/sdb1 /sdb1  
-t:指定文件系统类型

$ mount -o remount,noatime,data=writeback,barrier=0,nobh /dev/sdb  
remount:重新挂载文件系统。
noatime:每次访问文件时不更新文件的访问时间。
async:适用缓存，默认方式。

$ fuser -m /dev/sdb  
查看文件系统占用的进程

$ lsof /dev/sdb  
查看正在被使用的文件，losf命令是list open file的缩写

---------------
## 运行 iostat，查看 await 平均等待时间，await 越高则说明磁盘负荷越大。

1.  **哪个进程访问了磁盘？**  
    通过 iotop 找到产生大量 IO 的进程。
1.  **进程访问了哪些文件？**  
    通过 strace 跟踪高 IO 进程与文件操作相关的系统调用，查看其调用详情和耗时时长，找到耗时长的读写操作。并通过其操作的文件描述符 fd 映射回磁盘上的文件，了解为什么需要读写这些文件，进而查看是否可以优化。