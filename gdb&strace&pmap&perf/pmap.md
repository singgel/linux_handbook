# pmap  
pmap内存分析工具，可以查看进程的内存映像信息。常用来检查是否存在内存泄露 问题  
  
-x   extended       Show the extended format. 显示扩展格式  
-d  device         Show the deviceformat.   显示设备格式  
-q   quiet          Do not display some header/footerlines. 不显示头尾行  
-V   show version   Displays version of program. 显示版本  
————————————————  
  
mapped 表示该进程映射的虚拟地址空间大小，也就是该进程预先分配的虚拟内存大小，即ps出的vsz  
writeable/private  表示进程所占用的私有地址空间大小，也就是该进程实际使用的内存大小       
shared 表示进程和其他进程共享的内存大小  
  
内存泄露检测方法：  
A）VIRT/VSZ或者writeable/private (pmap –d输出）如果在做重复的操作过程中一直保持稳定增长，那么一定有内存泄露。  
B) RSS只能作为参考，不能用来确定是否有内存泄露。  
  
while true; do pmap -d 4027 | tail -1; sleep 2; done  
  
# pstack  
pstack用来跟踪进程栈，这个命令在排查进程问题时非常有用，比如我们发现一个服务一直处于work状态（如假死状态，好似死循环），使用这个命令就能轻松定位问题所在；可以在一段时间内，多执行几次pstack，若发现代码栈总是停在同一个位置，那个位置就需要重点关注，很可能就是出问题的地方；  
————————————————  
  
pt-pmp  
pt-pmp可以对生成的pstack输出文件进行排序分析，也可以直接对进程进行堆栈排序分析。  
pt-pmp对堆栈信息排序：  
pt-pmp pstack.info | less  
也可以直接执行pt-pmp：  
pt-pmp --pid 4522  