---
createTime: 2025-04-22
---
参考CSDN：https://blog.csdn.net/z13615480737/article/details/142597946



## 下载 Docker
阿里镜像源：https://mirrors.aliyun.com/docker-ce/linux/static/stable/x86_64/
下载 [docker-26.1.4.tgz](https://mirrors.aliyun.com/docker-ce/linux/static/stable/x86_64/docker-26.1.4.tgz?spm=a2c6h.25603864.0.0.6ea015ac6mlCaf)

解压后
```sh
cp docker/* /usr/bin/
```

启动docker守护进程
```sh
dockerd &
```

验证是否启动成功
```sh
docker info
```

注册到服务 (需要提前把 docker kill 掉)
```
vim /usr/lib/systemd/system/docker.service
```

```
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