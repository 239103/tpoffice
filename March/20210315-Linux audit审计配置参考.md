## 简介
Linux audit是linux安全体系的重要组成部分，是一种“被动”的防御体系。在内核里有内核审计模块，记录系统中的各种动作和事件，比如系统调用，文件修改，执行的程序，系统登入登出和记录所有系统中所有的事件，它的主要目的是方便管理员根据日记审计系统是否允许有异常，是否有入侵等等，也就是把和系统安全有关的事件记录下来

## 安装使用
### 安装命令
`sudo yum install auditd`

### 启动auditd服务

`sudo service auditd start`

### 查看当前auditd服务状态
`sudo service auditd status`
- 或者

`sudo auditctl -s`

### 重启auditd服务
`sudo service auditd restart`
- 或者
`sudo service auditd reload`

### 配置
auditd的配置文件为/etc/audit/audit下的auditd.conf 和audit.rules，auditd.conf主要是定义了auditd服务日志和性能等相关配置，audit.rules才是定义规则的文件
```
cat > /etc/audit/rules.d/audit.rules <<EOF
# This file contains the auditctl rules that are loaded
# whenever the audit daemon is started via the initscripts.
# The rules are simply the parameters that would be passed
# to auditctl.
# First rule - delete all
-D
# Increase the buffers to survive stress events.
# Make this bigger for busy systems
-b 102400
# Feel free to add below this line. See auditctl man page
# 针对执行命令的审计
-a exit,always -F arch=b64 -S umask -S chown -S chmod
-a exit,always -F arch=b64 -S unlink -S rmdir
-a exit,always -F arch=b64 -S setrlimit
# 针对文件访问的审计
-w /etc/group -p wa
-w /etc/passwd -p wa
-w /etc/shadow -p wa
-w /etc/sudoers -p wa
EOF
```
### 使配置规则生效
`sudo augenrules --load`

### 查看审计日志
`sudo ausearch -k keyword`