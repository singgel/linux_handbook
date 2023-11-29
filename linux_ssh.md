## 检查SSH密钥
使用SSH协议，您可以连接和验证远程服务器和服务。使用SSH密钥，您可以在每次访问时无需提供用户名或密码即可连接到Coding。

检查已有的SSH密钥
在生成新的SSH密钥之前，可以先检查一下是否可以使用已有的ssh密钥。

打开你的终端窗口。
输入ls -al ~/.ssh查看已有SSH密钥：
$ ls -al ~/.ssh
检查目录列表是否存在已有的公共SSH密钥。一般情况下，公钥的文件名是以下之一：
> id_dsa.pub
> id_ecdsa.pub
> id_ed25519.pub
> id_rsa.pub
如果秘钥文件已存在，则可以重新生成，也可以复用之前的pub文件粘贴到coding

## 生成SSH密钥
打开你的终端窗口,windows环境建议安装gitbash：https://git-scm.com/download/win

使用以下命令，将会以你的邮箱作为标签创建一个新的SSH密钥，注意运行下面命令生成sshkey时不限目录，key都会生成在~/.ssh目录下。

$ ssh-keygen -t ed25519 -C "工作邮箱，比如：zhansan@jd.com"
> Generating public/private ed25519 key pair.
当系统提示您“输入要保存密钥的文件”时，按Enter键。将接受默认文件位置。如下：
> Enter a file in which to save the key (/Users/you/.ssh/id_ed25519): [Press enter]
在提示符下，输入安全密码，即可生成一个新的ssh密钥。
注意： 如果生成ssh秘钥的时候输入了密码，则每次clone/push代码时都需要输入密码才行。
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]


## 复制SSH密钥。
如果您的SSH密钥文件的名称与示例代码的名称不同，请修改文件名以匹配您当前的设置。复制密钥时，请勿添加任何换行符或空格。

macOS:
 $ pbcopy < ~/.ssh/id_ed25519.pub

GNU/Linux (requires the xclip package):
 xclip -sel clip < ~/.ssh/id_ed25519.pub

Windows Command Line:
 type %userprofile%\.ssh\id_ed25519.pub | clip

Git Bash on Windows / Windows PowerShell:
 cat ~/.ssh/id_ed25519.pub | clip
 提示
如果命令行复制不起作用，您可以找到隐藏.ssh文件夹，使用文本编辑器中打开id_ed25519.pub文件，复制内容，注意复制的时候去掉字符串结尾的换行和空格

## 验证SSH的连通性
测试连接时，如果之前生成秘钥时输入了密码，则您需要使用密码验证此操作，密码是您之前创建的SSH密钥密码。
打开你的终端窗口。
使用以下命令：
$ ssh -vT coding.jd.com
# Attempts to ssh to Coding

可能会常出现以下提示信息：
> debug1: Authentication succeeded (publickey).
> Authenticated to coding.jd.com ([172.20.39.5]:22). 
> debug1: channel 0: new [client-session]
> debug1: Entering interactive session.
> debug1: pledge: network
> debug1: Sending environment.
> debug1: Sending env LANG = zh_CN.UTF-8
> shell request failed on channel 0

说明
表示使用ssh的方式，连通性已经验证通过:
Authenticated to coding.jd.com ([172.20.39.5]:22).
由于server端限制了shell request的操作，故会看到如下报错信息（可忽略该报错信息）：
shell request failed on channel 0
如果联通性失败报错 padding error: need 68 block 16 mod 4 请重新生成sshkey进行配置，极少数情况生成的key由于字节对齐的原因会造成认证失败，重新生成一套密钥就行了。

如果从办公网访问coding出现连接22端口超时的情况：
connect to address 172.20.39.5 port 22: Connection time out
请申请开通22（ssh）端口的联通性
