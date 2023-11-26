Docker环境安装： 略

mrdoc镜像拉取： https://www.mrdoc.fun/doc/18/ ， 使用镜像mrdoc-alpine ，内网限制镜像只能通过docker save -o xx.tar mrdoc-alpine:0.8.1 导出， docker load -i xx.tar导入。

部署机器： 10.172.50.150  

运行容器： docker run -d --name mrdoc -p 10086:10086 -v ~/mrdoc:/app/MrDoc/config -v ~/mrdoc/media:/app/MrDoc/media jonnyan404/mrdoc-alpine:0.8.1 

管理员账号：admin wutong

数据目录:  /root/mrdoc

数据库配置: /root/mrdoc/config.ini , vim config.ini

```shell
[site]
# True表示开启站点调试模式，False表示关闭站点调试模式
debug = False
[database]
# engine，指定数据库类型，接受sqlite、mysql、oracle、postgresql
engine = mysql
# name表示数据库的名称
name = mrdoc
# user表示数据库用户名
user = pre
# password表示数据库用户密码
password = xxxxxx
# host表示数据库主机地址
host = 测试库ip
# port表示数据库端口
port=3306
[selenium]
# 在Windows环境下测试或使用，请配置driver = Chrome
# driver = Chrome
# 如果系统无法正确安装或识别chromedriver，请指定chromedriver在计算机上的绝对路径
driver_path = /usr/lib/chromium/chromedriver
```


创建数据库： create database mrdoc character set utf8mb4 collate utf8mb4_general_ci;

容器重启： docker stop mrdoc, docker start mrdoc 

访问地址 :http://10.172.50.150:10086/


替换docker容器内的静态文件logo.svg,保存为新的镜像 ： docker commit -a "fubin5617" -m "replace logo.svg" 5394a3f43b37 ydzt/mrdoc-v1

![Alt text](MrDoc%E9%83%A8%E7%BD%B2%E5%92%8C%E5%AE%B9%E5%99%A8%E5%86%85%E4%BF%AE%E6%94%B9/image.png)

数据如何备份 : 1. 备份10.172.50.150机器 /root/mrdoc目录， 2. 备份10.172.58.26 测试数据库mrdoc 