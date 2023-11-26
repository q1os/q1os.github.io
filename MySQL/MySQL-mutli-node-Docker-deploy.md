> 环境：centos7，安装docker

## 下载mysql镜像

docker pull mysql:5.7

## 挂载路径约定

/var/mysql-version-port

```markdown
mkdir -p /var/mysql-5.7-3306
```

创建 conf，data，log目录
```markdown
mkdir -p /var/mysql-5.7-3306/conf
mkdir -p /var/mysql-5.7-3306/data
mkdir -p /var/mysql-5.7-3306/log
```



## 启动容器

```markdown
docker run -d -it -p 3306:3306 --name mysql-5.7-3306 \
--restart=always \
-v /var/mysql-5.7-3306/conf/my.cnf:/etc/my.cnf \
-v /var/mysql-5.7-3306/conf:/etc/mysql \
-v /var/mysql-5.7-3306/log:/var/log/mysql \
-v /var/mysql-5.7-3306/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=test@123 \
mysql:5.7
```

> 虚拟机注意关闭防火墙：systemctl stop firewalld && systemctl disable firewalld