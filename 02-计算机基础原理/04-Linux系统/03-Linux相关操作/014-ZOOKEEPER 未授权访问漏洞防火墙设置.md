---
createTime: 2025-05-15
---
# （尚未验证） - 2025-05-15


# 需求描述
1、仅白名单内的IP地址可以访问2181端口，其他IP地址不可以访问2181端口。  
2、其他端口不做限制



# 操作步骤
### step1 查看现有防火墙规则，备份。
```shell
#坚持防火墙是否开启
sudo iptables -L

#查看现有规则
sudo firewall-cmd --list-all

# 备份当前规则
sudo iptables-save > /home/klapp/iptables-backup
```



### step2 启动防火墙，访问限制由默认拒绝，变更为默认允许。
仅白名单内的IP地址可以访问2181端口；其他IP地址不可以访问2181端口。

```shell
#启动防火墙
systemctl start firewalld
systemctl status firewalld

```

切换到trusted 分区,该分区默认放行所有请求

```powershell
#查看默认区域	
firewall-cmd --get-default-zone
#临时切换默认区域	
sudo firewall-cmd --set-default-zone=trusted
#为接口分配区域	,替换成本机网卡号
sudo firewall-cmd  --permanent --zone=trusted --change-interface=ens192

sudo firewall-cmd --reload
```



### 配置过滤规则，放行白名单内的2181端口，拦截所有2181端口
```shell
# 创建名为 zk_cluster_ips 的 ipset（类型为 hash:ip）
sudo firewall-cmd --permanent --new-ipset=zk_cluster_ips --type=hash:ip

# 添加 IP 10.5.2.131~146 到 ipset
for ip in {131..146}; do
  sudo firewall-cmd --permanent --ipset=zk_cluster_ips --add-entry=10.5.2.$ip
done
#补充一个10.5.2.241服务器
sudo firewall-cmd --permanent --ipset=zk_cluster_ips --add-entry=10.5.2.241
sudo firewall-cmd --permanent --ipset=zk_cluster_ips --add-entry=10.129.24.122
# 重新加载防火墙
sudo firewall-cmd --reload

#创建自定义链
sudo firewall-cmd --permanent --direct --add-chain ipv4 filter zookeeper_chain
#挂载到 IN_trusted 链
sudo firewall-cmd --permanent --direct --add-rule ipv4 filter IN_trusted 0 -j zookeeper_chain
#添加规则到 zookeeper_chain  放行白名单内的2181
sudo firewall-cmd --permanent --direct --add-rule ipv4 filter zookeeper_chain 0 -m set --match-set zk_cluster_ips src -p tcp --dport 2181 -j ACCEPT
  #拒绝所有2181
sudo firewall-cmd --permanent --direct --add-rule ipv4 filter zookeeper_chain 1 -p tcp --dport 2181 -j REJECT 
# 重新加载防火墙
sudo firewall-cmd --reload

# 查看 ipset 内容
sudo firewall-cmd --info-ipset=zk_cluster_ips


```

<font style="color:rgb(51, 51, 51);">‌</font>

# 删除操作
```python
sudo firewall-cmd --permanent --ipset=zk_cluster_ips --remove-entry=10.5.2.141
```



```powershell
# 清空链中的所有规则
sudo firewall-cmd  --permanent --direct --remove-rules ipv4 filter zookeeper_chain
#删除从 IN_trusted 链跳转到 zookeeper_chain 的规则
sudo firewall-cmd --permanent --direct --remove-rule ipv4 filter IN_trusted 0 -j zookeeper_chain
#最后删除自定义链本身
sudo firewall-cmd --permanent --direct --remove-chain ipv4 filter zookeeper_chain

```









