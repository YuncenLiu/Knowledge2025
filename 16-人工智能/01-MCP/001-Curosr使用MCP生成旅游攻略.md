---
createTime: 2025-05-20
---
cursor 使用 MySQL、File、高德地图 这三个MCP，通过 agent 生成旅游攻略

### 安装依赖

stdio 在本地又两种环境，一种 Python 、一种 TypeScript，分别对应 uvx、npx
验证方式
```sh
PS C:\Users\xiang\Desktop\mail> npx --version
8.1.0
PS C:\Users\xiang\Desktop\mail> uvx --version
uvx 0.7.6 (7f3e94a09 2025-05-19)
```
下载方式
```sh
# npx 需要 Node 20+
# pip 下载 uvx
pip install uv
```


### 在cursor中配置局部mcp环境

![](images/Pasted%20image%2020250520230132.png)

windows 环境下
```json
{
    "mcpServers": {
        "mcp-server-mysql": {
            "command": "npx",
            "args": [
                "-y",
                "@smithery/cli@latest",
                "run",
                "@benborla29/mcp-server-mysql",
                "--key",
                "c710d12f-30fe-49af-b4f1-96caa76b2d0d",
                "--profile",
                "familiar-snipe-T9kwNX"
            ]
        },
        "amap-maps": {
            "command": "npx",
            "args": [
                "-y",
                "@amap/amap-maps-mcp-server"
            ],
            "env": {
                "AMAP_MAPS_API_KEY": "a511aa8a41bc04ba3720384d046a0cce"
            }
        },
        "fs": {
            "command": "cmd",
            "args": [
                "/c",
                "npx",
                "-y",
                "@smithery/cli@latest",
                "run",
                "@bunasQ/fs",
                "--key",
                "c710d12f-30fe-49af-b4f1-96caa76b2d0d"
            ]
        }
    }
}
```



### 注册高德开放平台
[https://console.amap.com/](https://console.amap.com/) ，获取 key 值

![](images/Pasted%20image%2020250520225816.png)

### 使用MCP平台

#### mysql-mcp-server

[https://smithery.ai/](https://smithery.ai/)

![](images/Pasted%20image%2020250520225935.png)


#### FileSystem

[https://smithery.ai/server/@bunasQ/fs](https://smithery.ai/server/@bunasQ/fs)

### 高德地图MCP

[https://mcp.so/zh/server/amap-maps/amap](https://mcp.so/zh/server/amap-maps/amap)

![](images/Pasted%20image%2020250520230357.png)


等待 mcpserver 中的所有 mcp 都显示绿灯，使用 agent 模式，然后选用可用的大模型语言进行沟通

```sh
现在交给你一个任务，编写一个北京一日游出行攻略
1、从高德地图的MCP服务中获取北京站到天安门、天安门到颐和园、颐和园到南锣鼓巷的地铁路线，并保存在数据库cloud的表中mcp_subway_trips中，写入之前判断这张表是否存在如果不存在则创建。
2、从高德地图的MCP中获取颐和园、南锣鼓巷附近的美食信息，每处获取三家美食店铺信息，并将相应的信息存入mcp_location_food中，写入之前判断这张表是否存在如果不存在则创建。
3、将工作目录 F:\studySpace\NLP\MCP\cursor_mcp 下创建一个新文件夹，命名为“北京旅行”在其中创建两个txt，分别从数据库中将两个表内容提取出来存放进去
4、最后根据txt中的内容，生成一个精美的 html 前端展示页面，并保存在该目录下
```

然后持续点下一步，下一步，即可实现

![](images/Pasted%20image%2020250520230638.png)


> [!Note]
> 需要注意的是，MCP好像无法执行 DDL 语句，可用手动在数据库中创建，让 MCP 执行插入即可
> ![](images/Pasted%20image%2020250520230805.png)