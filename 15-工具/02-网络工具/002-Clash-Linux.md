---
createTime: 2025-04-27
---
远程 Linux环境 无法访问 Github 等资源，希望通过代理方式访问的解决方案

Github：[wnlen/clash-for-linux](https://github.com/wnlen/clash-for-linux)

因为一开始无法下载，所以先通过能访问的服务器下载后，压缩上传到服务器上，假设部署在这个路径
![](images/Pasted%20image%2020250427154333.png)
将本地 clashX-pro 的配置文件上传到 `conf/config.yaml`

从泡泡Dog平台中获取到订阅定制，写入 `/root/clash-for-linux/.env` 文件中

```sh
vim .env
```
![](images/Pasted%20image%2020250427154514.png)

启动服务

```sh
bash start.sh
```

![](images/Pasted%20image%2020250427154554.png)
```sh
source /etc/profile.d/clash.sh

proxy_on
```

检查端口情况

```sh
netstat -tln | grep -E '9090|789.'
```

```sh
env | grep -E 'http_proxy|https_proxy'
```


![](images/Pasted%20image%2020250427154720.png)

## Web端访问

浏览器访问：`http://xxx:9090/ui/`

![](images/Pasted%20image%2020250427154901.png)

点击 ADD 进入
![](images/Pasted%20image%2020250427154931.png)



### 停止程序

```sh
cd clash-for-linux

bash shutdown.sh


proxy_off
```

![](images/Pasted%20image%2020250427155037.png)