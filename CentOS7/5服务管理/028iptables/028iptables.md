# iptables

## iptables 规则
* iptables -t filter 命令 规则链 规则
  * 命令
    * -L
    * -A -I (添加 A：在末尾添加 I：插入到第一条)
    * -D -F -P（删除某个 清除全部 默认）
    * -N -X -E （自定义规则链 删除自定义规则链 重命名自定义规则）
    ```
    man iptables
    # 查看已有规则
    iptables -t filter -L
    iptables -t filter -nL
    iptables -t filter -vnL
    iptable -t nat -vnL

    # 添加规则，设置某个ip可以进入主机
    iptables -t filter -A INPUT -s 10.0.0.1 -j ACCEPT
    ```

## filter 过滤规则
### source
```
iptables -vnL

# 允许
iptables -A INPUT -s 10.0.0.2 -j ACCEPT
# 拒绝
iptables -A INPUT -s 10.0.0.2 -j DROP
# 注意顺序，先 ACCEPT 后 DROP = 允许，所以 DROP 要在 ACCEPT 之前
iptables -I INPUT -s 10.0.0.2 -j DROP
```
* 默认规则（policy）
  * 全部允许，只阻止个别 ip（policy ACCEPT）
  * 全部阻止，只允许个别 ip（ploicy DROP）
  ```
  iptables -P INPUT DROP
  ```
* 清除所有规则（-F）（只能修改自添加规则，默认规则不会被改变）
```
iptables -F
```
* 清除某个（第N个）规则（-D）
* 网段
```
iptables -A INPUT -s 10.0.0.0/24 -j ACCEPT
```
### destination
* OUTPUT
* FORWARD

### prot 协议
* tcp 
* udp
* icmp
```
# 指定协议
-p tcp
# 指定协议和访问端口
-p tcp --dport 80

# 组合
iptables -t filter -A INPUT -i eth0 -s 10.0.0.2 -p tcp --dport 80 -j ACCEPT
iptables -t filter -A INPUT -j DROP
``` 

## nat 网络地址转换
```
iptables -t nat -vnL
```
* iptables -t nat 命令 规则链 规则
  * PREROUTING 目的地址转换
  * POSTROUTING 源地址转换
```
# 目的地址转换：将请求转给 10.0.0.1
iptables -t nat -A PREROUTING -i eth0 -d 114.115.116.117 -p tcp --dport 80 -j DNAT --to-destination 10.0.0.1

# 源地址转换 将地址伪装成 111.112.113.114 发出去
iptables -t nat -A POSTROUTING -s 10.0.0.0/24 -o eth1 -j SNAT --to-source 111.112.113.114
```
* 配置文件
  * 没有记入配置文件的规则，重启之后就消失了
  * ``` /etc/sysconfig/iptables ```
  * CentOS6
    * service iptables save | start | stop | restart
  * CentOS7
    * yum install uptables-services
