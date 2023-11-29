#查看开机信息
dmesg
#查看文件版本
md5sum

## 基础
使用命令行工具，如top、htop或ps等。
top：在终端中输入top命令，然后按"M"键按照内存占用排序进程。
htop：如果已安装htop工具，可以输入htop命令来查看进程和其内存占用情况。
ps：使用ps aux --sort -rss命令来列出进程并按照内存占用排序。

# 查看内存占用前10名的程序
ps aux | sort -k4,4nr | head -n 5
top -p 2913
cat /proc/2913/status 
# 对于内存泄露
dmesg -T| grep oom-killer
# 显示已经运行的进程的CPU亲和性
taskset -p pid
# 法获取某进程中运行中的线程数量（PID指的是进程ID）
ls /proc/PID/task | wc -l

# 使用cgroups进行内存限制