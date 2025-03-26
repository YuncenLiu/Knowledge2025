---
createTime: 2021-11-07
---
前提：需要Root权限


1. 给文件赋予写权限

```sh
chmod +w /etc/sudoers
```

2. 编写 /etc/sudoers 文件

假设想给 `xiang` 用户添加 sudo 权限，编写

```
%xiang ALL = (ALL)NOPASSWD:ALL
```

保存退出即可生效