---
createTime: 2025-04-16
---
扫描固定IP常用端口，写到 prot.txt 文件中
```sh
nmap 10.5.3.124 -oN port.txt

nmap 114.113.226.201 -oN port.txt
```

扫描端口范围 
```sh
nmap 10.5.3.178 -p 1-10000 -oN port.txt
nmap 114.113.226.201 -p 1-60000 -oN port.txt
```

扫描一个IP段
```sh
nmap 10.5.3.170-180 -p 1-10000 -oN 3-port.txt
```

扫描一个段的固定几个端口
```sh
# open 和 close 都会获取
nmap 10.5.3.0/24 -p 80,443,8080 -oN http-port.txt

# 只获取 open
nmap 10.5.3.0/24 -p 80,443,8080 --open -oN open-http-ports.txt

# 获取几个固定IP
nmap 10.5.1.29 10.5.1.30 -p 80,443,8080 --open -oN open-http-ports.txt
```


