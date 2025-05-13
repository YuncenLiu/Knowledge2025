---
createTime: 2025-05-12
---
## Docker 构建并部署 umami

docker 搭建 umami 流量分析工具，参考 [CSDN](https://blog.csdn.net/nxg0916/article/details/144163344)

官网Github 项目地址 [GitHub](https://github.com/umami-software/umami)
这里用官网的项目，使用 node:v20+ 版本忽略下载依赖包，然后根据 Github 首页 README.md 使用指定（空的）数据库进行构建，表结构会自动加载到数据中。



云环境的只有 14天免费，到期估计就没了。 [cloud-在线环境](https://us.umami.is/websites/ff0e8852-157f-438b-97e5-e567e37627cd)

个人博客：自定义数据源启动：[https://www.zywvvd.com/notes/tools/umami/umami/](https://www.zywvvd.com/notes/tools/umami/umami/)

下载 GeoLite2-City.mmdb 问题：我提交了 [Issus](https://github.com/umami-software/umami/issues/3327#issuecomment-2872738867)

个人 docker 版本

```sh
docker pull registry.cn-beijing.aliyuncs.com/yuncenliu/umami:mysql-1.0
docker run -itd --name umami -p 13010:3000 registry.cn-beijing.aliyuncs.com/yuncenliu/umami:mysql-1.0
```

最终成功部署在 [https://umami.liuyuncen.com/login](https://umami.liuyuncen.com/login)  云环境

