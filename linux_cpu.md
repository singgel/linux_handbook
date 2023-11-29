#查看开机信息
dmesg
#查看文件版本
md5sum

-------------
#要查看cpu波动情况的，尤其是多核机器上
mpstat -P 2,3,4,5
#该命令可间隔2秒钟采样一次CPU的使用情况，每个核的情况都会显示出来
mpstat -P ALL 2 5

#查看内存占用前10名的程序
ps aux | sort -k4,4nr | head -n 10
top -p 2913
cat /proc/2913/status 
#对于内存泄露
dmesg | grep oom-killer
#显示已经运行的进程的CPU亲和性
taskset -p pid
#法获取某进程中运行中的线程数量（PID指的是进程ID）
ls /proc/PID/task | wc -l