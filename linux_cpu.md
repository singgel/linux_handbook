#查看开机信息
dmesg
#查看文件版本
md5sum

-------------
## 要查看cpu波动情况的，尤其是多核机器上
mpstat -P 2,3,4,5
## 该命令可间隔2秒钟采样一次CPU的使用情况，每个核的情况都会显示出来
mpstat -P ALL 2 5

## 查看内存占用前10名的程序
ps aux | sort -k4,4nr | head -n 10
top -p 2913
cat /proc/2913/status 
## 对于内存泄露
dmesg | grep oom-killer
## 显示已经运行的进程的CPU亲和性
taskset -p pid
## 法获取某进程中运行中的线程数量（PID指的是进程ID）
ls /proc/PID/task | wc -l

-------------
## 使用 top 等命令查看 CPU 使用率和负载是否过高。

1.  **内核空间占了大多数 CPU？**  
    通过 top 命令查看内核占用 CPU 比例是否过大。
1.  **内核处理了很多中断吗？**  
    使用 procinfo 或 cat /proc/interrupts 查看中断次数和频率，以及造成中断数量较高的设备。
1.  **内核的时间花在哪了？**  
    使用 oprofile 分析哪些内核函数消耗大量时间，并查询这些函数的功能，确定它们处于哪个子系统（内存、网络、磁盘等）并了解可能被调用的原因。如果这些函数是设备特定的，尝试着找出为什么需要使用这种特定的设备（尤其是在1.2中造成高中断的设备），或许就可以判断出哪里出了问题。
1.  **用户空间占了大多是 CPU？**  
    通过 top 命令查看用户态是否占了大量 CPU。
1.  **哪个进程占用了大多数 CPU？**  
    通过 top 命令进程排序列表确定占用大量 CPU 的进程。
1.  **进程在内核还是用户空间花费了时间？**  
    使用 time 命令查看进程在内核和用户空间花费的时间，这里不一定非得任意一方非得占大多数时间。如进程在内核占用超过25%以上的时间，说明内核也排查是重点。
1.  **进程在哪些系统调用上花费了较多时间？**  
    使用 strace/oprofile 查看进程调用了哪些系统调用和找出主要耗时时长的系统调用，通过减少系统调用次数或者更改性能更好的系统调用来提升性能。
1.  **进程在哪些函数上花费了时间？**  
    使用 ltrace/oprofile 确定哪些函数消耗了大多数时间。如果函数调用次数过多，则检查是否存在不必要的调用次数，如 for 循环判断条件里不断调用某个函数或者 debug 日志里调某个函数得到一个字符串序列。如果单次调用就已经很耗时，使用 oprofile/cachegrind 分析函数是否存在热点代码大量 cache 缺失，通过调整数据结构或调整代码提高热点代码 cache 命中。