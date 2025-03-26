---
createTime: 2025-03-21
---
Linux 需要切换到 oracle 用户

```sh
sqlplus / as sysdba
```



```sql
-- 查询用户状态
SELECT USERNAME, ACCOUNT_STATUS, EXPIRY_DATE FROM DBA_USERS WHERE USERNAME = 'CASHVALUE';

-- 如果账户被锁定，可以解锁 
ALTER USER CASHVALUE ACCOUNT UNLOCK; 
-- 恢复为默认配置（如有过期配置）
ALTER USER CASHVALUE PROFILE DEFAULT;

-- 执行完这个，密码过期就自动延期了，在查询用户状态，日期就会是在以后
-- 注意这个密码必要带引号 ‘ 
ALTER USER CASHVALUE IDENTIFIED BY new_password;
```


重新登录的时候，如果

```sh
sqlplus fktest01/fktestabc@10.5.3.102:1521/fktestdb
```

这样登录不进去，就执行 回车后，输入用户名和密码

```sh
sqlplus fktest01@10.5.3.102:1521/fktestdb
```

