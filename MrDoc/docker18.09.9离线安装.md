## docker18.09.9安装
- docker下载地址：docker安装包下载地址：https://download.docker.com/linux/static/stable/x86_64/
- 复制docker-18.09.9.tgz到服务器上 /root路径，解压：tar xzvf docker-18.09.9.tgz ，得到一个docker文件夹
- cp docker/* /usr/bin/ 把docker程序拷贝到系统路径
- 添加docker开机启动, vim /etc/systemd/system/docker.service

```markdown
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target
 
[Service]
Type=notify
ExecStart=/usr/bin/dockerd
ExecReload=/bin/kill -s HUP $MAINPID
LimitNOFILE=infinity
LimitNPROC=infinity
TimeoutStartSec=0
Delegate=yes
KillMode=process
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s
 
[Install]
WantedBy=multi-user.target
```

- 给文件赋执行权限 ： chmod +x /etc/systemd/system/docker.service
- 重新加载系统配置：systemctl daemon-reload
- 设置docker开机启动：systemctl enable docker.service
- 启动docker ： systemctl start docker
- 检查docker运行状态： systemctl status docker