#查看开机信息
dmesg
#查看文件版本
md5sum

ping <目标地址> -S <出口IP地址>
ping <目标地址> -I <出口接口名称>

-------------
## 查看iptables的变化（流量没有离开本机）
watch -n 1 "iptables -t filter -nvL "

-------------
## 抓包(https://www.cnblogs.com/jiujuan/p/9017495.html)
## 抓包监控流量
tcpdump -i eth0 port 1314 -nnn
## 抓指定的目的地
tcpdump -i eth0 dst host 169.254.169.254

## 抓包发送给目的服务器10.60.18.80的请求包和响应包
## 请求包抓取
tcpdump -n -X -i bond0 dst host 10.60.18.80
## 响应包抓取
tcpdump -n -X -i bond0 src host 10.60.18.80
## 12500端口上抓包
tcpdump -n -X -i any port 12500  icmp

-------------
## 表名为 nat、filter 或是 mangle 三张表中的一张，如果不填默认是filter表
iptables -vnL -t [表名] --line-numbers
## 列出指定表的指定链的规则
iptables -t [表名] -vnL [链名]

-------------
## 查看连接数
netstat -n | awk '/^tcp/ {++state[$NF]} END {for(key in state) print key,"t",state[key]}'
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
## 发现有SYNs包被丢弃，即有可能是半连接队列溢出
#连接队列包括两种，一个是半连接队列（syn queue），一个是全连接队列（accept queue）；
netstat -s | grep LISTEN
## 查看stock的连接
ss -xln | grep tmp
## 查看socket文件的具体信息
stat /tmp/stream.sock
ncat -U /tmp/tbsocket1

--------------
## 使用 ethool 查看网卡的最大流量限制 ，并通过 iptraf 查看流经端口的流量是否饱和。

1.  **网络设备产生了大量错误？**  
    使用 ifconfig/ip 命令查看网络接口是否产生大量错误，如果是可能是硬件配置的有问题，联系网管帮忙排查解决。
1.  **网络设备存流量类型是什么？**  
    使用 iptraf 查看流量类型（协议/端口号）。
1.  **是否有进程处理该类型的流量？**  
    使用 netstat 查看是否有进程在流经该网络端口的流量。
1.  **哪个远程系统发送了流量？**  
    如果没有指定进程在处理这个流量，可能来自网络上其他系统的流量攻击。可使用 etherape/wireshark 尝试跟踪或者找网管咨询。
1.  **哪个套接字在处理流量？**  
    在确定了处理流量的进程后，使用 strace/lsof 找到是哪个套接字产生了这些通信流量。


