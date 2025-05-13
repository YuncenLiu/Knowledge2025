---
createTime: 2025-04-21
---


```sh
sudo yum groupinstall "Development Tools"
sudo yum install pcre-devel openssl-devel
```

进入 Nginx-1.xxx 文件中，执行

```sh
./configure --with-stream --with-http_ssl_module --with-pcre 
```

手动安装

```sh
make 
sudo make install
```

