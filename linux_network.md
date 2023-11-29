#查看开机信息
dmesg
#查看文件版本
md5sum

-------------
#查看iptables的变化（流量没有离开本机）
watch -n 1 "iptables -t filter -nvL "

-------------
#抓包(https://www.cnblogs.com/jiujuan/p/9017495.html)
#抓包监控流量
tcpdump -i eth0 port 1314 -nnn
#抓指定的目的地
tcpdump -i eth0 dst host 169.254.169.254

#抓包发送给目的服务器10.60.18.80的请求包和响应包
#请求包抓取
tcpdump -n -X -i bond0 dst host 10.60.18.80
#响应包抓取
tcpdump -n -X -i bond0 src host 10.60.18.80
#12500端口上抓包
tcpdump -n -X -i any port 12500  icmp

-------------
#表名为 nat、filter 或是 mangle 三张表中的一张，如果不填默认是filter表
iptables -vnL -t [表名] --line-numbers
#列出指定表的指定链的规则
iptables -t [表名] -vnL [链名]


-------------
#查看连接数
netstat -n | awk '/^tcp/ {++state[$NF]} END {for(key in state) print key,"t",state[key]}'
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
#发现有SYNs包被丢弃，即有可能是半连接队列溢出
#连接队列包括两种，一个是半连接队列（syn queue），一个是全连接队列（accept queue）；
netstat -s | grep LISTEN
#查看stock的连接
ss -xln | grep tmp
#查看socket文件的具体信息
stat /tmp/stream.sock
ncat -U /tmp/tbsocket1




