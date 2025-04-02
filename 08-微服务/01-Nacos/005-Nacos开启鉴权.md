---
createTime: 2025-03-27
---
调整 Nacos 的 application.properties 配置文件

```yml
### If turn on auth system:
### 开启鉴权，默认为 false
nacos.core.auth.enabled=true

# 登录用户名和密码 nacos/nacos
nacos.core.auth.server.identity.key=SecretKey0123456789012345678901234567890123456789012345678901234567890
nacos.core.auth.server.identity.value=SecretKey0123456789012345678901234567890123456789012345678901234567890

### 开启鉴权，并自定义 token (必须要这么长，否则无法启动)
nacos.core.auth.plugin.nacos.token.secret.key=Zw4iH9kFj3qXpV/Ta2u7nP6mYb1LcR5vO0yE8dSgKtM=
```

SpringBoot 配置

```yml
spring:
  cloud:
    nacos:
      discovery:
        username: nacos
        password: nacos
      config:
        username: nacos
        password: nacos
```
