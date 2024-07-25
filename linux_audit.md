## 作用  
使用audit监控目录并查看CentOS上的文件操作记录  

## 案例
定位原因的方案使用内核audit选项    
线上的natgw的内核audit功能都关了。华北没有进行bios和重启优化的设备目前还没有关  
1. 修改内核参数去掉 audit=0选项  
2. 启动audit服务  
3. 使用命令监控/tmp/core.sock被删除事件  
auditctl -w /tmp/test -p wa -k xx-test-deleted  
4. 查看日志  
``` sh
[root@T19 rules.d]# tailf /var/log/audit/audit.log  
type=SYSCALL msg=audit(1711001319.010:4640): arch=c000003e syscall=263 success=yes exit=0 a0=ffffffffffffff9c  
a1=21650c0 a2=0 a3=7fff577422a0 items=2 ppid=19270 pid=19941 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0  
fsgid=0 tty=pts2 ses=2 comm="rm" exe="/usr/bin/rm" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023  
key="xx-test-deleted"  
type=CWD msg=audit(1711001319.010:4640): cwd="/root"  
type=PATH msg=audit(1711001319.010:4640): item=0 name="/tmp/" inode=67160136 dev=fd:00 mode=041777 ouid=0 ogid=0  
rdev=00:00 obj=system_u:object_r:tmp_t:s0 objtype=PARENT cap_fp=0000000000000000 cap_fi=0000000000000000 cap_fe=0  
cap_fver=0  
type=PATH msg=audit(1711001319.010:4640): item=1 name="/tmp/test" inode=67166959 dev=fd:00 mode=0100644 ouid=0 ogid=0  
rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=DELETE cap_fp=0000000000000000 cap_fi=0000000000000000  
cap_fe=0 cap_fver=0  
type=PROCTITLE msg=audit(1711001319.010:4640): proctitle=726D002D69002F746D702F74657374  
```
### audit功能生效验证  
1. 查看服务启动正常systemctl status auditd.service  
2. 查看配置加载成功auditctl -l  
3. 查看审计功能开启auditctl -s  
