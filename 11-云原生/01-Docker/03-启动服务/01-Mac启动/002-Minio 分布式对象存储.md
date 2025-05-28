---
createTime: 2021-12-24
---


[toc]

### 前言

感觉比腾讯 COS 对象好用



## docker 部署运行

官网：[https://docs.min.io/](https://docs.min.io/)
个人镜像：`registry.cn-beijing.aliyuncs.com/yuncenliu/minio:2025-03-12`

执行脚本：

```sh
mkdir -p /root/docker/minio/data
```

```sh
docker run \
  -p 19000:9000 \
  -p 19001:9001 \
  --name minio \
  -v /root/docker/minio/data:/data \
  -e "MINIO_ROOT_USER=root" \
  -e "MINIO_ROOT_PASSWORD=11111111" \
  quay.io/minio/minio server /data --console-address ":9001"
```

⚠️注意：密码至少八位

完整命令

```sh
docker run -p 19000:9000 -p 19001:9001 --name minio -v /root/docker/minio/data:/data -e "MINIO_ROOT_USER=root" -e "MINIO_ROOT_PASSWORD=11111111" quay.io/minio/minio server /data --console-address ":9001"
```



本地客户端永久保留文件

Super 服务器中

mc文件在 `/root/docker` 路径下

```sh
./mc config host add minio http://101.201.81.193:19000 root 11111111
```

```sh
./mc policy get minio/red-pavilion
```

```sh
./mc policy set download minio/red-pavilion
```







## 带 Https 的Minio

```sh
docker run 
	-p 11900:9000 
	-p 11901:9001 
	--name minio-https 
	-e "MINIO_ACCESS_KEY=admin" 
	-e "MINIO_SECRET_KEY=admin123456" 
	-v /root/docker/minio/data:/data 
	-v /root/docker/minio/conf:/root/.minio 
  quay.io/minio/minio server /data 
	--console-address ":9001"
```

完整命令

```sh
docker run \
	-p 11900:9000 \
	-p 11901:9001 \
	--name minio-https \
	-e "MINIO_ACCESS_KEY=admin" \
	-e "MINIO_SECRET_KEY=admin123" \
	-v /root/docker/minio/data:/data \
	-v /root/docker/minio/conf:/root/.minio \
  quay.io/minio/minio server /data \
	--console-address ":9001"
	--address ":9000"
```



```sh
docker run \
	-p 11900:9000 \
	-p 11901:9001 \
	--name minio-https \
	-e "MINIO_ACCESS_KEY=admin" \
	-e "MINIO_SECRET_KEY=admin123" \
	-v /root/docker/minio/data:/data \
	-v /root/docker/minio/conf:/root/.minio \
  quay.io/minio/minio server /data \
	--console-address ":9001"
	--address "liuyuncen.com:9000"
```

```sh
docker run \
	-p 11900:9000 \
	-p 11901:9001 \
	--name minio-https \
	-e "MINIO_ACCESS_KEY=admin" \
	-e "MINIO_SECRET_KEY=admin123" \
	-v /root/docker/minio/data:/data \
	-v /root/docker/minio/conf:/root/.minio \
  quay.io/minio/minio server /data \
	--console-address ":9001"
	--address "172.17.0.2:9000"
```

```sh
docker run \
	--network=host \
	-p 11900:9000 \
	-p 11901:9001 \
	--name minio-https \
	-e "MINIO_ACCESS_KEY=admin" \
	-e "MINIO_SECRET_KEY=admin123" \
	-v /root/docker/minio/data:/data \
	-v /root/docker/minio/conf:/root/.minio \
  quay.io/minio/minio server /data \
	--console-address ":9001"
	--address "liuyuncen.com:9000"
```



## mc 客户端使用

启动 Minio

```sh
minio server /Users/xiang/xiang/compile/minio/data --address :9000 --console-address :9001 &
```


Mac 端下载
```sh
https://dl.minio.org.cn/client/mc/release/darwin-amd64/mc
```

Mac 环境使用

注册一个可用的 Minio
```sh
./mc alias set dapi_uat http://cloud-minio-data-10-5-3-176:8082  xQfGhPRPG0bJ9YW4HiB6 22BzgLhqfGjWAxIZfsOOfCmexNRVGRn8JiYXRhVR


mc alias set dapi_uat http://10.5.3.178:9000 minioadmin minioadmin
```


查看这个对象存储状态
```sh
./mc admin info dapi_uat
```
![](images/Pasted%20image%2020250407093047.png)

查看目录
```sh
./mc ls dapi_uat/
```

以目录形式显示
```sh
./mc tree dapi_uat
```
![](images/Pasted%20image%2020250407093223.png)

下载 mc get minio对象 本地对象
```sh
./mc ls dapi_uat/exchange-module/upload/20250403/2387235f59c377e75f936262363f3440.zip /Users/xiang/Downloads/

mc get dapi_uat/exchange-module/upload/20250527/每月月初再保数据-143656.zip ./
```


上传 mc cp -r 本地对象 minio对象 (-r 支持文件夹上传)
```sh
./mc cp /Users/xiang/Downloads/Flink-1.17/flink-1.17.0-bin-scala_2.12.tgz dapi_uat/exchange-module/upload/互联网高薪必读书单/
```