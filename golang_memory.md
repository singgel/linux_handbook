## import包
 _ "net/http/pprof"

## 打开端口
http.ListenAndServe(":6060", nil)

Alloc：已分配的内存字节数。
TotalAlloc：程序运行期间分配的所有内存字节数。
Sys：系统使用的内存字节数。
Lookups：指针查找的次数。
Mallocs：分配的堆对象数。
Frees：释放的堆对象数。
HeapAlloc：堆上已分配的内存字节数。
HeapSys：堆系统的内存字节数。
HeapIdle：堆中空闲的内存字节数。
HeapInuse：堆中正在使用的内存字节数。
HeapReleased：回收到操作系统的内存字节数。
HeapObjects：堆中存在的对象数。
Stack：栈的内存使用情况。
MSpan：内存分配的堆栈和其他元数据。
MCache：内存分配器的缓存。
BuckHashSys：哈希表的系统内存。
GCSys：垃圾回收元数据的内存。
OtherSys：其他系统申请的内存。
NextGC：下次垃圾回收的目标内存字节数。
LastGC：上次垃圾回收的时间戳。
PauseNs：每次垃圾回收的停顿时间。
PauseEnd：每次垃圾回收停顿的结束时间戳。
NumGC：垃圾回收的次数。
NumForcedGC：强制触发的垃圾回收次数。
GCCPUFraction：垃圾回收占用的CPU时间比例。
DebugGC：是否启用了垃圾回收的调试模式。
MaxRSS：程序运行期间的最大峰值内存使用量。
这些数据可以用于监控程序的内存使用情况和垃圾回收性能。