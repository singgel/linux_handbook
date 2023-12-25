# perf  
perf是Linux下的一款性能分析工具，能够进行函数级与指令级的热点查找。  
  
Linux性能计数器是一个新的基于内核的子系统，它提供一个性能分析框架，比如硬件（CPU、PMU(Performance Monitoring Unit)）功能和软件(软件计数器、tracepoint)功能。  
  
通过perf，应用程序可以利用PMU、tracepoint和内核中的计数器来进行性能统计。它不但可以分析制定应用程序的性能问题（per thread），也可以用来分析内核的性能问题，当然也可以同时分析应用程序和内核，从而全面理解应用程序中的性能瓶颈。  
  
使用perf，可以分析程序运行期间发生的硬件事件，比如instructions retired、processor clock cycles等；也可以分析软件时间，比如page fault和进程切换。  
————————————————  
![Alt text](image-1.png)  
  
2、使用示例一  
2.1 采集mysqld进程60秒的性能数据  
sudo perf record -a -F 99 -g -p $(pgrep -x mysqld) -- sleep 60  
参数：-F 99Hz采集,它的执行频率是 99Hz（每秒99次）  
2.2 持续采集mysqld性能数据，直到ctrl+c取消  
sudo perf record -a -F 99 -g -p $(pgrep -x mysqld)  
2.3 数据加工  
sudo perf script > perf.script  
2.4 生成火焰图  
安装FlameGraph(该工具也可以分析如dtrace、gdb等等生成的trace)  
git clone https://github.com/brendangregg/FlameGraph.git   
  
3、使用示例二（通过top来分析性能消耗）  
perf top -p `pidof mysqld`  
  
第一列：符号引发的性能事件的比例，默认指占用的cpu周期比例。  
第二列：符号所在的DSO(Dynamic Shared Object)，可以是应用程序、内核、动态链接库、模块。  
第三列：DSO的类型。[.]表示此符号属于用户态的ELF文件，包括可执行文件与动态链接库)。[k]表述此符号属于内核或模块。  
第四列：符号名。有些符号不能解析为函数名，只能用地址表示。  