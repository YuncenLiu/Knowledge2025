---
createTime: 2025-03-14
---
设置SWAP分区，榨干云服务器性能

查看内存和缓存

```sh
free -lm
```

![](images/Pasted%20image%2020250314230959.png)

正常情况的云服务器是没有开启 swap 的

## 1、创建 Swap 分区

```sh
mkdir -p /data

dd if=/dev/zero of=/data/swap bs=1M count=4096
# 本案例SWAP分区大小为4G
```

| 参数    | 含义                           |
| ----- | ---------------------------- |
| if    | if=文件名：输入文件名，默认为标准输入。即指定源文件  |
| of    | of=文件名：输出文件名，默认为标准输出。即指定目的文件 |
| bs    | 同时设置读入/输出的块大小为bytes个字节       |
| count | 仅拷贝blocks个块，块大小等于ibs指定的字节数   |
![](images/Pasted%20image%2020250314231222.png)


格式化并启用 swap 分区

```sh
mkswap /data/swap
# Setting up swapspace version 1, size = 2260712 KiB
# no label, UUID=9ea02e7b-e672-4d43-9698-4fe99bfeee1a
```

```sh
swapon /data/swap
# swapon: /data/swap: insecure permissions 0644, 0600 suggested.
```

此时 `free -lm` 即可看到 Swap 分区，但是要开机自动挂载还需要继续配置

```sh
vim /etc/fstab

/data/swap swap swap defaults 0 0
```

![](images/Pasted%20image%2020250314231516.png)


此时永久分区的 Swap 就设置好了

## 调整 vm.swappiness 

控制换出运行时内存的相对权重，swappiness参数值可设置范围在0到100之间，参数取值对如何使用swap分区有很大联系。 此参数值越低，就会让Linux系统尽可能的少用swap分区，多用内存；参数值越高，则使内核更多的去使用swap空间。默认值swappiness=60

```sh
cat /proc/sys/vm/swappiness
```

临时调整

```sh
sysctl vm.swappiness=30
```

永久配置

```sh
vi /etc/sysctl.conf   
# 添加如下内容
vm.swappiness=30
```

加载配置

```sh
sysctl -p
```

## 测试硬盘 iops 性能 


```sh
yum install -y ioping

ioping -RD -w 5 /mnt
```

![](images/Pasted%20image%2020250314231950.png)