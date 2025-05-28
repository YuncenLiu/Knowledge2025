> 创建于 2022年4月3日
>
> 作者：想想

[toc]



### 查询Python版本

默认情况下，Linux都会自带Python，可以利用 `python --version` 查看版本信息

```sh
python --version
```

### 下载 Python 3.6.5

[Python3.6.5.tar](https://www.python.org/ftp/python/3.6.5/Python-3.6.5.tgz)

下载地址：https://www.python.org/ftp/python/3.6.5/Python-3.6.5.tgz

也可以登录https://www.python.org/downloads/source/，找到自己需要的版本

```sh
https://www.python.org/ftp/python/3.9.22/Python-3.9.22.tgz
```

### 安装

解压

```sh
tar -zxvf Python-3.6.5.tgz
```

准备编译环境

```sh
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make
```


> [!Note]
> 这里建议去 02-问题 看一下安装 OpenSSL1.1.1，后续可能会出现此升级问题


需要考虑的问题：
1. 存在部分组件无法使用需要升级 OpenSSL1.1.1 参考 [001-OpenSSL 1.1.1问题 解决](../02-问题/001-OpenSSL%201.1.1问题%20解决.md)，然后
2. 使用 pyinstall 时缺少依赖 Python library not found: libpython3.10.so.1.0，参考 [CSDN](https://blog.csdn.net/qq_36991535/article/details/144446824) 构建时添加 `--enable-shared`  参数

编译安装

```sh
./configure --prefix=/usr/local/Python-3.6.5 
# /usr/local/Python-Python-3.8.2    这个路径是Python 即将安装的位置
make && make install


# 升级 SSL 安装
# vim Modules/Setup
# 修改配置 214 行的 SSL=/usr/local/openssl-1.1.1

./configure --enable-shared --prefix=/usr/local/Python-3.9.22
make -j4 && make install
echo "/usr/local/lib" | sudo tee /etc/ld.so.conf.d/python3.conf
sudo ldconfig

/usr/local/Python-3.9.22/bin/python3.9 -c "import ssl; print(ssl.OPENSSL_VERSION)"
# OpenSSL 1.1.1  11 Sep 2018
```

![](images/Pasted%20image%2020250527141412.png)


删除原先的软连接

```sh
rm -rf /usr/bin/python
```

建立新的软连接

```sh
ln -s /usr/local/Python-3.6.5/bin/python3.6 /usr/bin/python3
ln -s /usr/local/Python-3.6.5/bin/pip3 /usr/bin/pip3

ln -s /usr/local/Python-3.9.22/bin/python3.9 /usr/bin/python3
ln -s /usr/local/Python-3.9.22/bin/pip3 /usr/local/bin/pip3
```



### 测试

```sh
[root@super ~]# python --version
Python 3.8.2
```

