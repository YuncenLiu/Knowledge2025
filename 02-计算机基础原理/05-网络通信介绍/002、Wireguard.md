---
createTime: 2025-04-08
source: https://www.bilibili.com/video/BV1uT411w7Tq
---
# 基于 Wireguard 组网

官网地址：[https://www.wireguard.com/quickstart/](https://www.wireguard.com/quickstart/)

Wireguard 不区分客户端与服务端，在任何设备上安装了 Wireguard 都会被视为一个节点

搭建目标： 通过内网穿透，进行端口访问

## 安装 Wireguard

解决 wireguard-dkms 下载失败的情况
```sh
curl -Lo /etc/yum.repos.d/wireguard.repo https://copr.fedorainfracloud.org/coprs/jdoss/wireguard/repo/epel-7/jdoss-wireguard-epel-7.repo
```


```sh
yum install wireguard-dkms wireguard-tools -y
```


![](images/Pasted%20image%2020250408142913.png)


### 服务器B 192.168.58.8

开启 IP 转发
```sh
cat /etc/sysctl.conf

# net.ipv4.ip_forward=1

# 确认参数
sysctl -p
```

设置权限
```sh
chmod 755 /etc/wireguard
```

调整目录默认权限
```sh
umask 077
```

生成服务器密钥
```sh
wg genkey > server.key
wg pubkey < server.key > server.key.pub
```

生成客户端1密钥
```
wg genkey > client1.key
wg pubkey < client1.key > client1.key.pub
```


配置文件
```sh
echo "
[Interface]
PrivateKey = iA37UoXh5rTu4SE10i7wNN/dLp+TkHD304G9fqHkB3M= # 填写本机的privatekey 内容
Address = 10.0.8.1 #本机虚拟局域网IP

PostUp   = iptables -A FORWARD -i wg33 -j ACCEPT; iptables -A FORWARD -o wg33 -j ACCEPT; iptables -t nat -A POSTROUTING -o ens33 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg33 -j ACCEPT; iptables -D FORWARD -o wg33 -j ACCEPT; iptables -t nat -D POSTROUTING -o ens33 -j MASQUERADE
#注意eth0需要为本机网卡名称

ListenPort = 50814 # 监听端口
DNS = 8.8.8.8
MTU = 1420
[Peer]
PublicKey =  m2p3YIav+P9Zs4keMIGbFSdwzIeaED2K+4YJBAZFWjI=  #自动client1的公钥
AllowedIPs = 10.0.8.10/32 #客户端所使用的IP" > wg33.conf
```


启动 wireguard
```sh
wg-quick up wg33
```

![](images/Pasted%20image%2020250408150318.png)

查看本地UDP端口 , `ss` 是 `netstat` 的缩写
```sh
ss -anu | grep 50814
```

firewalld开放端口
```sh
firewall-cmd --add-port=50814/udp --permanent && firewall-cmd --reload
```


### 服务器C 192.168.59.3

列一下密钥信息
```sh
server.key
iA37UoXh5rTu4SE10i7wNN/dLp+TkHD304G9fqHkB3M=

server.key.pub
PPg8IU6rSbsjuejmm/auRm1qomw2nGpIwYwQbJRe83I=

client1.key
cKmU7dJktYoQYGGU0YgxxOrtwbhJHzHVOuJGWh1QLUA=

client1.key.pub
m2p3YIav+P9Zs4keMIGbFSdwzIeaED2K+4YJBAZFWjI=
```

在 192.168.59.3 的  `/etc/wireguard/wg33.conf`
```sh
[Interface]
PrivateKey = cKmU7dJktYoQYGGU0YgxxOrtwbhJHzHVOuJGWh1QLUA= #此处为client1的私钥
Address = 10.0.8.10 #此处为peer规定的客户端IP
MTU = 1500

[Peer]
PublicKey = PPg8IU6rSbsjuejmm/auRm1qomw2nGpIwYwQbJRe83I= #此处为server的公钥
AllowedIPs = 10.0.8.0/24 #此处为允许的服务器IP
Endpoint = 192.168.59.8:50814 #服务器对端IP+端口
```

启动
```sh
wg-quick up wg33
```

![](images/Pasted%20image%2020250408163454.png)
查看 wireguard 链接信息
```sh
wg
```

服务端 B服务器 192.168.58.8：
![](images/Pasted%20image%2020250408163631.png)
客户端1 C服务器 192.168.59.3
![](images/Pasted%20image%2020250408163700.png)


验证1：192.168.59.3 -> 访问 -> 192.168.58.8:8080  (失败)
验证2：192.168.59.3 -> 访问 -> 10.0.8.0:8080  (成功)
![](images/Pasted%20image%2020250408164256.png)

### 服务器A 192.168.58.9 

通过并入 服务端B，组成新的网络 10.0.8.11，此时，A、B、C 可以通过 wg33 网络肆意纵横

```sh
wg genkey > client2.key
# qNuyLQpBQuOW7YZ2mPl3/hxhL6qV9NJehZdVggr7dFU=
wg pubkey < client2.key > client2.key.pub
# ANP6TaD/sevU2SI1t9bFs2lIagAjHcNAa4ZZM2C1B0g=
```


B 服务器新增内容 `/etc/wireguard/wg33.conf`
```conf
[Peer]
PublicKey =  ANP6TaD/sevU2SI1t9bFs2lIagAjHcNAa4ZZM2C1B0g=  #自动client2的公钥
AllowedIPs = 10.0.8.11/32 #客户端所使用的IP
```

停止 wireguard 、 重启
```sh
wg-quick  down wg33
wg-quick  up wg33
```

在 192.168.58.9 服务器 `/etc/wireguard/wg33.conf`  添加如下内容
```conf
[Interface]
PrivateKey = qNuyLQpBQuOW7YZ2mPl3/hxhL6qV9NJehZdVggr7dFU= #此处为client2的私钥
Address = 10.0.8.11 #此处为peer规定的客户端IP
MTU = 1500

[Peer]
PublicKey = PPg8IU6rSbsjuejmm/auRm1qomw2nGpIwYwQbJRe83I= #此处为server的公钥
AllowedIPs = 10.0.8.0/24 #此处为允许的服务器IP
Endpoint = 192.168.59.8:50814 #服务器对端IP+端口
```



## Docker 启动 WireGuard

参考 [CSDN文章](https://blog.csdn.net/weixin_63767408/article/details/145285306) ，需要 Linux 核心5.9以上

docker 镜像来源于：`ghcr.io/wg-easy/wg-easy`

获取 HASH密码，[github issus](https://github.com/wg-easy/wg-easy/issues/1622) 中提到的密码问题
```sh
docker run --rm -it ghcr.io/wg-easy/wg-easy wgpw 'xiang'
docker run --rm -it registry.cn-beijing.aliyuncs.com/yuncenliu/repo:wg-easy_ghcr.io_wg-easy_202402 wgpw 'xiang'
```


```sh
docker run -d \
  --name=wg-easy \
  -e WG_HOST=39.105.177.10 \
  -e PASSWORD_HASH='$2a$12$9cRyl7ZykxeO1arjf5fFBuYpCwanZr/h/ircXmU26fCARa8AyY5xa' \
  -e WG_DEFAULT_ADDRESS=10.0.8.x \
  -e WG_DEFAULT_DNS=114.114.114.114 \
  -e WG_ALLOWED_IPS=10.0.8.0/24 \
  -e WG_PERSISTENT_KEEPALIVE=25 \
  -p 51820:51820/udp \
  -p 51821:51821/tcp \
  -v ~/.wg-easy:/etc/wireguard \
  --cap-add=NET_ADMIN \
  --cap-add=SYS_MODULE \
  --sysctl="net.ipv4.conf.all.src_valid_mark=1" \
  --sysctl="net.ipv4.ip_forward=1" \
  --restart unless-stopped \
  registry.cn-beijing.aliyuncs.com/yuncenliu/repo:wg-easy_ghcr.io_wg-easy_202402
```

![](images/Pasted%20image%2020250409011154.png)



Mac 系统中注册到 Wireguard 中，打开 WebStrome 运行项目
