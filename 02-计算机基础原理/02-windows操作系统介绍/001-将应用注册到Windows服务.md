---
createTime: 2025-02-28
resource: https://github.com/winsw/winsw/releases
---
[toc]

## 1、将 Nacos 注册到 Windows 服务

下载 GitHub 资源：https://github.com/winsw/winsw/releases

我这里选择 v2.11.0、WinSW-x64.exe 版本，重命名为 nacos.exe

放在 nacos/bin 目录下，同时新建 nacos.xml 文件

```xml
<service>
	<id>nacos</id>
	<name>nacos</name>
	<description>Nacos服务</description>
	<executable>C:\soft\nacos-2.5.0\bin\startup.cmd</executable>
	<stopexecutable>C:\soft\nacos-2.5.0\bin\shutdown.cmd</stopexecutable>
	<logpath>C:\soft\nacos-2.5.0\logs\</logpath>
	<log mode="roll"></log>
</service>
```

解释：我这里 C:\soft\nacos-2.5.0 就是 Nacos Home 目录

在当前目录下执行 cmd 命令

```sh
nacos.exe install # 安装服务 
nacos.exe uninstall # 卸载服务
```

注册成功

![](images/Pasted%20image%2020250228131355.png)
