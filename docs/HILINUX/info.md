# 查看系统信息

```sh
$ uname -a # 查看本机架构
Linux (none) 4.19.41 #1 SMP Tue Dec 14 16:52:41 CST 2021 aarch64 GNU/Linux
```

```sh
$ cat /proc/cpuinfo # 查看CPU 共四核
processor       : 0
BogoMIPS        : 48.00
Features        : fp asimd evtstrm aes pmull sha1 sha2 crc32 cpuid
CPU implementer : 0x41
CPU architecture: 8
CPU variant     : 0x0
CPU part        : 0xd03
CPU revision    : 4
…
```

```sh
$ cat /proc/meminfo # 查看内存
MemTotal:         494172 kB
MemFree:          459152 kB
```

```sh
$ df -h # 查看存储（这个是之前的一个板子 新板子存储肯定是够的）
Filesystem                Size      Used Available Use% Mounted on
/dev/root                 5.7G      5.6G    107.5M  98% /
devtmpfs                239.1M         0    239.1M   0% /dev
tmpfs                   241.3M         0    241.3M   0% /run
```

```sh
$ ifconfig # 查看本机网络配置 可以看到eth0/eth1是可以连接的两个网口
eth0      Link encap:Ethernet  HWaddr 32:7A:FA:5C:ED:DE
          inet addr:192.168.1.10  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::307a:faff:fe5c:edde/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:736 errors:0 dropped:0 overruns:0 frame:0
          TX packets:159 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:67170 (65.5 KiB)  TX bytes:20130 (19.6 KiB)
          Interrupt:41
eth1      Link encap:Ethernet  HWaddr 5E:ED:B7:1D:50:FB
          inet addr:192.168.10.68  Bcast:192.168.10.255  Mask:255.255.255.0
          UP BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
          Interrupt:45
```

```sh
$ echo $SHELL # 查看当前使用的shell
/bin/sh
```
