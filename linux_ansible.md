-------------
一、并行性和shell命令command|script|shell
#重启webservers主机组的所有机器，每次重启10 台
ansible webservers -a "/sbin/reboot" -f 10
#以ju 用户身份在webservers组的所有主机运行foo 命令
ansible webservers -a "/usr/bin/foo" -u ju
#以ju 用户身份sudo 执行命令foo（--ask-sudo-pass (-K) 如果有sudo 密码请使用此参数）
ansible webservers -a "/usr/bin/foo" -u ju --sudo [--ask-sudo-pass]
#也可以sudo 到其他用户执行命令非root
ansible webservers -a "/usr/bin/foo" -u username -U otheruser [--ask-sudo-pass]
#默认情况下，ansible 使用的module 是command，这个模块并不支持shell 变量和管道等，若想使用shell 来执行模块，请使用-m 参数指定shell 模块
#使用shell 模块在远程主机执行命令或脚本
ansible dbservers -m shell -a 'echo $TERM'
ansible dbservers -m shell -a '/tmp/test.sh'
#script命令模块，在远程主机执行主控端本地的脚本文件，相当于scp+shell
ansible dbservers -m script -a '/tmp/test.sh 111  222'
二、传输文件copy|file
#拷贝本地的/etc/hosts 文件到myserver主机组所有主机的/tmp/hosts（空目录除外）,如果使用playbooks 则可以充分利用template 模块
ansible myserver -m copy -a "src=/etc/hosts dest=/tmp/hosts mode=600 owner=ju group=ju"
#file 模块允许更改文件的用户及权限
ansible webservers -m file -a "dest=/srv/foo/a.txt mode=600"
ansible webservers -m file -a "dest=/srv/foo/b.txt mode=600 owner=ju group=ju"
#使用file 模块创建目录，类似mkdir -p
ansible webservers -m file -a "dest=/path/to/c mode=755 owner=ju group=ju state=directory"
#使用file 模块删除文件或者目录
ansible webservers -m file -a "dest=/path/to/c state=absent"
三、获取远程文件信息stat
ansible webservers -m stat -a "path=/etc/password"
四、下载指定定url到远程主机get_url
ansible webservers -m get_url -a "url=  mode=0440 force=yes"
五、管理软件包yum
#确保acme 包已经安装，但不更新
ansible webservers -m yum -a "name=acme state=present"
#确保安装包到一个特定的版本
ansible webservers -m yum -a "name=acme-1.5 state=present"
#确保一个软件包是最新版本
ansible webservers -m yum -a "name=acme state=latest"
#确保一个软件包没有被安装
ansible webservers -m yum -a "name=acme state=absent"
#Ansible 支持很多操作系统的软件包管理，使用时-m 指定相应的软件包管理工具模块，如果没有这样的模块，可以自己定义类似的模块或者使用command 模块来安装软件包
六、用户和用户组user
#使用user 模块对于创建新用户和更改、删除已存在用户非常方便
ansible all -m user -a "name=foo password=<crypted password here>"
ansible all -m user -a "name=foo state=absent"
生成加密密码方法
mkpasswd --method=SHA-512
pip install passlib
python -c "from passlib.hash import sha512_crypt; import getpass; print sha512_crypt.encrypt(getpass.getpass())"
七、服务管理service
#确保webservers 组所有主机的httpd 是启动的
ansible webservers -m service -a "name=httpd state=started"
#重启webservers 组所有主机的httpd 服务
ansible webservers -m service -a "name=httpd state=restarted"
#确保webservers 组所有主机的httpd 是关闭的
ansible webservers -m service -a "name=httpd state=stopped192.168.116"
八、后台运行
#长时间运行的操作可以放到后台执行，ansible 会检查任务的状态；在主机上执行的同一个任务会分配同一个job ID
#后台执行命令3600s，-B 表示后台执行的时间
ansible all -B 3600 -a "/usr/bin/long_running_operation --do-stuff"
#检查任务的状态
ansible all -m async_status -a "jid=123456789"
#后台执行命令最大时间是1800s 即30 分钟，-P 每60s 检查下状态默认15s
ansible all -B 1800 -P 60 -a "/usr/bin/long_running_operation --do-stuff"
九、搜集系统信息setup
#通过命令获取所有的系统信息
#搜集主机的所有系统信息
ansible all -m setup
#搜集系统信息并以主机名为文件名分别保存在/tmp/facts 目录
ansible all -m setup --tree /tmp/facts
#搜集和内存相关的信息
ansible all -m setup -a 'filter=ansible_*_mb'
#搜集网卡信息
ansible all -m setup -a 'filter=ansible_eth[0-2]'
十、计划任务cron
ansible all -m cron -a 'name="jutest" hour="5" job="/bin/bash /tmp/test.sh"'
效果如下：
* 5 * * *  /bin/bash /tmp/test.sh
十一、挂载模块mount
ansible all -m mount -a 'name=/mnt src=/dev/sda5 fstype=ext4 opts=rostate=present'
十二、通过Ansible实现批量免密
ssh-keygen -t rsa
ansible webservers -i /etc/ansible/hosts  -m authorized_key -a "user=root key='{{ lookup('file','/root/.ssh/id_rsa.pub') }}'" -k
