---
createTime: 2025-03-15
---
配置日志格式
```
log_format main '$remote_addr - $remote_user [$time_local] "$request" '
    '$status $body_bytes_sent "$http_referer" '
    '"$http_user_agent" "$http_x_forwarded_for"';
```

解除文件限制，默认最大 1M，可以配置在 http 下全局可用，也可以配置在 server 下，对某一个服务可用
```
http{
	client_max_body_size 100M;
	...
```


隐藏版本号
```conf
http {
	server_tokens off;
	...
```

缓存时间
```
location ~ \.(gif|jpg|jpeg|png|bmp|ico) {
	root html;
	expires 1d;
}
```
验证缓存生效：`curl -I http://192.168.192.20/wzry.jpg`  看到 Cache-Controle: max-age=86400，缓存时间一般用于静态资源

设置防盗链
```
location ~* \.(gif|jpg|jpeg|png|bmp|ico) {
	valid_referers none blocked *.yuncen.com yuncen.com
	if($valid_referers){
		return 403
	}
}
```

> [!Note]
> **valid_referers** ：设置信任的网站，可以正常使用图片；
> **blocked** ：允许不是 http:// 开头的，不带协议的请求访问资源；