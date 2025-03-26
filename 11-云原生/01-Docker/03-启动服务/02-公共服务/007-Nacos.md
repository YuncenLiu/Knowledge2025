---
createTime: 2025-03-14
---
```sh
docker pull registry.cn-beijing.aliyuncs.com/yuncenliu/nacos:v2.5.0

# 先启动一下，获取配置文件
docker run -itd --name nacos docker pull registry.cn-beijing.aliyuncs.com/yuncenliu/nacos:v2.5.0

mkdir -p /data/nacos

docker cp nacos:/home/nacos/logs /data/nacos/
docker cp nacos:/home/nacos/conf /data/conf/
# 这样文件就保存到本地了
# 该修改配置文件就修改配置文件，该创建数据库就创建数据库
```

```yaml
db.num=1
db.url.0=jdbc:mysql://xxxx:3388/nacos?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=Asia/Shanghai
db.user.0=nacos
db.password.0=nacos
```

调整完成后，最后启动

```sh
docker run -itd \
--name nacos  \
-p 8848:8848  -p 9848:9848 -p 9849:9849 \
--privileged=true \
-e JVM_XMS=125m \
-e JVM_XMX=125m \
-e MODE=standalone \
-v /data/nacos/logs/:/home/nacos/logs \
-v /data/nacos/conf/:/home/nacos/conf/ \
registry.cn-beijing.aliyuncs.com/yuncenliu/nacos:v2.5.0
```