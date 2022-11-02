# 板卡联网

可以IP转发、或者直接插网线。

## 本机启动squid服务联网

这里使用squid在本机的3128端口启动squid服务，然后在板子的Linux机器中设置http_proxy，具体步骤可以查看：https://blog.csdn.net/liumiaocn/article/details/108629944 和 https://blog.csdn.net/db3265613/article/details/125850102

### HTTP

本机：

```
$ brew install squid
$ brew --prefix # 查看brew的默认安装路径 从而找到squid的配置文件
$ ls /usr/local/etc/squid*
$ code /usr/local/etc/squid.conf # VSCode打开该文件 修改59行的`http_access deny all`为`http_access allow all`，顺便看到62行的`http_port 3128`
$ brew services start squid
$ brew services list | grep squid
```

板卡：

```
$ export http_proxy=http://192.168.10.123:3128 # 其中`192.168.10.123`为本机网线连接的网段的IP
$ wget http://www.baidu.com/ && cat index.html # 可以看到已经能上网了
```

### HTTPS

TODO  https://www.shuzhiduo.com/A/B0zqwLAK5v/  https://blog.csdn.net/BigBoy_Coder/article/details/107717950  都不行

## 板卡直接接入清华校园网

TODO  先开DHCP  https://blog.csdn.net/cocoron/article/details/105230180

## 板卡的默认路由设置为本机

```
$ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
192.168.1.0     0.0.0.0         255.255.255.0   U     0      0        0 eth0
192.168.10.0    0.0.0.0         255.255.255.0   U     0      0        0 eth1
$ ip route
192.168.1.0/24 dev eth0 scope link  src 192.168.1.10
192.168.10.0/24 dev eth1 scope link  src 192.168.10.68

$ ip route add default via 192.168.10.123  dev eth0
```

不行

## 使用公司梯子

开开Clash，板卡Linux直接 `export http_proxy=http://192.168.10.123:1080` 即可上网（包括外网）。
