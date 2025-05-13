
## 检查Docker用户组

```sh
grep docker /etc/group
```

如果没有的话再创建

```sh
groupadd docker
```

给用户名 `xiang` 添加到组

```sh
usermod -aG docker klapp
```

添加完成后，需要对该账户重新登录操作， `bash` 没有

最后是重启一下 docker (很重要)
```sh
systemctl restart docker
```


如果还是不行

```sh
chmod 755 /var/run/docker.sock
```
