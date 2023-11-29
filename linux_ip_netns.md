-------------

ip netns exec port-y5juxi07st ifconfig ? mtu 1000 #修改mtu的大小
# 添加并启动虚拟网卡tap设备(https://juejin.cn/post/7057833934947614750)
ip tuntap add dev tap0 mode tap 
ip tuntap add dev tap1 mode tap 
ip link set tap0 up
ip link set tap1 up
# 配置IP
ip addr add 10.0.0.1/24 dev tap0
ip addr add 10.0.0.2/24 dev tap1
# 添加netns(https://segmentfault.com/a/1190000038171918)
ip netns add ns0
ip netns add ns1
# 将虚拟网卡tap0，tap1分别移动到ns0和ns1中
ip link set tap0 netns ns0
ip link set tap1 netns ns1

# 创建一对veth(https://typesafe.cn/posts/linux-veth-pair/)
ip link add veth0 type veth peer name veth1
# 将veth移动到netns中
ip link set veth0 netns ns0
ip link set veth1 netns ns1
# 启动
ip netns exec ns0 ip link set veth0 up
ip netns exec ns1 ip link set veth1 up
#修改路由出口为veth
ip netns exec ns0 ip route change 10.0.0.0/24 via 0.0.0.0 dev veth0
ip netns exec ns1 ip route change 10.0.0.0/24 via 0.0.0.0 dev veth1

# 添加网桥(https://typesafe.cn/posts/linux-bridge/)
brctl addbr br0
# 启动网桥
ip link set br0 up

# 新增三个netns
ip netns add ns0
ip netns add ns1
ip netns add ns2

# 新增两对veth
ip link add veth0-ns type veth peer name veth0-br
ip link add veth1-ns type veth peer name veth1-br
ip link add veth2-ns type veth peer name veth2-br

# 将veth的一端移动到netns中
ip link set veth0-ns netns ns0
ip link set veth1-ns netns ns1
ip link set veth2-ns netns ns2

# 将netns中的本地环回和veth启动并配置IP
ip netns exec ns0 ip link set lo up
ip netns exec ns0 ip link set veth0-ns up
ip netns exec ns0 ip addr add 10.0.0.1/24 dev veth0-ns

ip netns exec ns1 ip link set lo up
ip netns exec ns1 ip link set veth1-ns up
ip netns exec ns1 ip addr add 10.0.0.2/24 dev veth1-ns

ip netns exec ns2 ip link set lo up
ip netns exec ns2 ip link set veth2-ns up
ip netns exec ns2 ip addr add 10.0.0.3/24 dev veth2-ns

# 将veth的另一端启动并挂载到网桥上
ip link set veth0-br up
ip link set veth1-br up
ip link set veth2-br up
brctl addif br0 veth0-br
brctl addif br0 veth1-br
brctl addif br0 veth2-br


#ip link是与OSI七层模型的第二层数据链路层有关
ip link show                    # 显示网络接口信息
ip link set eth0 up             # 开启网卡
ip link set eth0 down            # 关闭网卡
ip link set eth0 promisc on      # 开启网卡的混合模式
ip link set eth0 promisc offi    # 关闭网卡的混合模式
ip link set eth0 txqueuelen 1200 # 设置网卡队列长度
ip link set eth0 mtu 1400        # 设置网卡最大传输单元
ip link | grep -E '^[0-9]' | awk -F: '{print $2}' #获取主机所有网络接口
ip -s link #显示所有网络接口的统计数据
ip -s -s link ls eth0 #获取一个特定网络接口的信息

#ip address (ip addr)就是与第三层网络层有关
ip addr show     # 显示网卡IP信息
ip addr add 192.168.0.1/24 dev eth0 # 为eth0网卡添加一个新的IP地址192.168.0.1
ip addr del 192.168.0.1/24 dev eth0 # 为eth0网卡删除一个IP地址192.168.0.1

ip route show # 显示系统路由
ip route add default via 192.168.1.254   # 设置系统默认路由
ip route list                 # 查看路由信息
ip route add 192.168.4.0/24  via  192.168.0.254 dev eth0 # 设置192.168.4.0网段的网关为192.168.0.254,数据走eth0接口
ip route add default via  192.168.0.254  dev eth0        # 设置默认网关为192.168.0.254
ip route del 192.168.4.0/24   # 删除192.168.4.0网段的网关
ip route del default          # 删除默认路由
ip route delete 192.168.1.0/24 dev eth0 # 删除路由
ip route get 119.75.216.20 #通过 IP 地址查询路由包从哪条路由来
ip route flush cache #刷新路由表

ip neigh list #显示邻居表