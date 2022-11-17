# 开发板连接电脑

板卡ARM端有两个接口，IP如下：

![](images-221102/nipc-network-interface.jpg)

## macOS

需要使用电脑连接，则需要先通过网线将板卡和电脑连接到一起；然后在电脑中设置IP地址为同一网段，如 `192.168.1.123` 或 `192.168.10.123`，子网掩码 `255.255.255.0`：

![](images-221102/macos-network.jpg)

点击右下角 `Apply`。

## Windows

打开 `设置 > Network & Internet > Change adapter options`，打开之后选择对应的网口，双击。

打开 `Properties`，双击 `Internet Protocol Version 4 (TCP/IPv4)`。在 `General` 下选择 `Use the following IP address`，填写即可。

![](images-221102/windows-network.png)

点击右下角 `OK`。

---

然后使用telnet连接即可：

![](images-221102/new-ifconfig.jpg)
