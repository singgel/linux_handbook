-------------
#rpm包使用
rpm -ivh ipm软件安装包
rpm -qa | grep mn-collector #查看版本
rpm -qlf /usr/local/bin/cc_controller_dr

-------------
# curl json格式化
curl https://news-at.zhihu.com/api/4/news/latest | python -m json.tool
/export/Shell/check_active_hostgroup.sh


-------------
#使用 SCP 将文件从远程复制到本地
scp <Username>@<IPorHost>:<PathToFile>   <LocalFileLocation>
