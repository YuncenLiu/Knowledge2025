---
createTime: 2025-05-21
---
之前因为公司限制使用 conda，所以这里不用 conda 安装虚拟环境，使用 brew，通过 brew list 可以看到有 python@3.13 环境如果没有就安装一个

```sh
brew install python@3.13
brew uninstall python@3.13
# 忽略删除
brew uninstall --ignore-dependencies python@3.13

# 获取更新安装
brew reinstall python@3.13

# 查看python13环境
brew info python@3.13
```

![](images/Pasted%20image%2020250521100209.png)

配置环境变量 `～/.bashrc`

```
alias python=python3
alias python=/usr/local/Cellar/python@3.13/3.13.3/bin/python3
alias pip=/usr/local/Cellar/python@3.13/3.13.3/bin/pip3
export PYTHON_HOME=/usr/local/Cellar/python@3.13/3.13.3
```

在更新 pip 时候出现了两个问题

### 缺少 libexpat 环境变量
![](images/Pasted%20image%2020250521100454.png)

原因是 libexpat 的库有问题，尽管使用 `brew reinstall expat` 安装了还是无法成功升级 pip
原因是没有环境变量

![](images/Pasted%20image%2020250521100633.png)

```sh
# 这里有历史的环境变量
export DYLD_LIBRARY_PATH=/usr/local/Cellar/expat/2.7.1/lib:/usr/local/oracle/instantclient_12_2:/lib:/usr/lib
```

### 使用 brew 安装的python在安装全局包时，系统会阻止

第一种方案就是使用虚拟环境。
第二种就是安装 pipx，然后强制安装

```sh
brew install pipx

# 绕过限制，强制安装
pip install --upgrade pip --break-system-packages
pip install setuptools

# 只为当前用户安装
pip install --upgrade pip --user
```

后续不管安装啥玩意儿，都得

```sh
pip install uv --break-system-packages
```

![](images/Pasted%20image%2020250521100930.png)