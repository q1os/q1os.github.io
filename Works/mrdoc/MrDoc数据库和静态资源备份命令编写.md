
## MrDoc数据库和静态资源备份命令编写

- 备份MrDoc MySQL数据库，mrdoc库
- 用户名和密码要连着写，端口单独用-P 大写

```shell
/usr/bin/mysqldump -h IP地址 -P 3405 -uusername -ppassword  \
    --default-character-set=utf8 mrdoc>  保存路径/mrdoc_mysql_`date +%Y%m%d%H%M%S`.sql
```

备份静态资源文件 10.172.50.150 /root/mrdoc目录

```shell
tar czf  mrdoc_static_`date +%Y%m%d%H%M%S`.tar.gz  /root/mrdoc
```

- 注意：如果本机没有禁止ssh权限，配置git sshkey的免密操作即可
- 生成公钥和私钥：ssh-keygen -t rsa -C “username@company.com”
- 如果行不通，可在sh脚本里输入密码操作，使用expect软件 ,需要注意头引入expect

```shell
#!/usr/bin/expect -f
set timeout 60
spawn git pull
expect "Username for 'http://gitlab.xxxxxx.com:8090':"
send "username\r"
expect "Password for 'http:/username@gitlab.company.com:8090':"
send "password\r"
interact  # interact的作用就是要将把控制权交给控制台
## expect eof
```

## 备份完整脚本

gitpull.sh : 自动拉取最新代码

```shell
#!/usr/bin/expect
set timeout 60
spawn git pull
expect "Username for 'http://gitlab.xxxxxx.com:8090':"
send "username\r"
expect "Password for 'http:/username@gitlab.company.com:8090':"
send "password\r"
expect eof
```

gitpush.sh：自动推送代码

```shell
#!/usr/bin/expect
set timeout 60
spawn git push
expect "Username for 'http://gitlab.xxxxxx.com:8090':"
send "username\r"
expect "Password for 'http:/username@gitlab.company.com:8090':"
send "password\r"
expect eof
```

自动dump数据库数据

```shell
#!/bin/bash
cd /root/mrdoc_backup/mobile-department-user-doc
/root/mrdoc_backup/gitpull.sh
/usr/bin/mysqldump -h IP地址 -P 3405 -uusername -ppassword  \
    --default-character-set=utf8 mrdoc> 保存路径/mrdoc_mysql_`date +%Y%m%d%H%M%S`.sql
git add .
git commit -m "mrdoc mysql数据备份提交"
/root/mrdoc_backup/gitpush.sh
```

自动压缩打包静态文件

```shell
#!/bin/bash
cd /root/mrdoc_backup/mobile-department-user-doc
/root/mrdoc_backup/gitpull.sh
## 绝对路径备份要f前面加P
tar -czvPf  保存路径地址/mrdoc_static_`date +%Y%m%d%H%M%S`.tar.gz  /root/mrdoc
git add .
git commit -m "mrdoc mysql数据备份提交"
/root/mrdoc_backup/gitpush.sh
```

## cron定时任务配置

起两个cron在两台机器跑，如果想放在一台机器，静态资源服务器IP可以安装一个mysql来使用mysqldump命令，数据推送到gitlab：http://gitlab.xxxxxx.com:8090/a32-ydzt/docs/mobile-department-user-doc

```shell
查看cron服务装状态： service crond status
修改cron文件：crontab -e
10点整执行脚本备份数据库：
0 10 * * * /root/mrdoc_backup/mrdoc_backup_mysql.sh> /root/mrdoc_backup/mrdoc_backup_mysql_task.log 2>&1
10点20分执行脚本备份静态资源：
20 10 * * * /root/mrdoc_backup/mrdoc_backup_static.sh>  /root/mrdoc_backup/mrdoc_backup_static_task.log 2>&1
```