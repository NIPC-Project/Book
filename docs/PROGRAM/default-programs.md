# 板卡默认安装程序

```sh
$ echo $PATH
/usr/bin:/usr/sbin:/bin:/sbin
```

```
$ /usr/bin
[            clear        envdir       hd           lpq          nc           pscan        setkeycodes  strings      top          unxz         whois
[[           cmp          envuidgid    head         lpr          nl           pstree       setsid       sum          tr           unzip        xargs
awk          comm         expand       hexdump      lsof         nmeter       pwdx         setuidgid    sv           traceroute   uptime       xxd
basename     crontab      expr         hexedit      lspci        nohup        readlink     sha1sum      svc          traceroute6  users        xz
bc           cryptpw      factor       hostid       lsscsi       nproc        realpath     sha256sum    svok         truncate     uudecode     xzcat
beep         cut          fallocate    id           lsusb        nslookup     renice       sha3sum      tac          tty          uuencode     yes
blkdiscard   dc           fgconsole    install      lzcat        od           reset        sha512sum    tail         ttysize      vlock
bunzip2      deallocvt    find         ipcrm        lzma         openvt       resize       showkey      taskset      udpsvd       volname
bzcat        diff         flock        ipcs         lzopcat      passwd       rpm2cpio     shred        tcpsvd       unexpand     w
bzip2        dirname      fold         iperf3       man          paste        runsv        shuf         tee          uniq         wall
cal          dos2unix     free         killall      md5sum       patch        runsvdir     smemcap      telnet       unix2dos     wc
chpst        du           ftpget       last         mesg         pgrep        rx           softlimit    test         unlink       wget
chrt         dumpleases   ftpput       less         microcom     pkill        script       sort         tftp         unlzma       which
chvt         eject        fuser        logger       mkfifo       pmap         seq          split        time         unlzop       who
cksum        env          groups       logname      mkpasswd     printf       setfattr     ssl_client   timeout      unshare      whoami

$ ls /usr/sbin
add-shell     chat          deluser       fbset         i2cdetect     inetd         nfsroot       powertop      remove-shell  svlogd
addgroup      chpasswd      dhcprelay     fdformat      i2cdump       killall5      nologin       rdate         rtcwake       telnetd
adduser       chroot        dnsd          fsfreeze      i2cget        loadfont      ntpd          rdev          sendmail      tftpd
arping        crond         ether-wake    ftpd          i2cset        lpd           partprobe     readahead     setfont       udhcpd
brctl         delgroup      fakeidentd    httpd         ifplugd       nbd-client    popmaildir    readprofile   setlogcons

$ ls /bin
arch           cpio           ethtool        himd.l         linux64        mknod          pidof          rmdir          stty           vi
ash            cttyhack       false          himm           ln             mktemp         ping           rpm            su             watch
base64         date           fatattr        hostname       login          more           ping6          run-parts      sync           zcat
btools         dd             fdflush        hush           ls             mount          pipe_progress  scriptreplay   tar
busybox        df             fgrep          ionice         lzop           mountpoint     printenv       sed            touch
cat            dmesg          fsync          iostat         makemime       mpstat         ps             setarch        true
chgrp          dnsdomainname  getopt         ipcalc         mkdir          mt             pwd            setpriv        udevadm
chmod          dumpkmap       grep           kbd_mode       mke2fs         mv             reformime      setserial      udevd
chown          echo           gunzip         kill           mkfs.ext2      netstat        resume         sh             umount
conspy         ed             gzip           link           mkfs.ext3      nice           rev            sleep          uname
cp             egrep          himd           linux32        mkfs.ext4      nuke           rm             stat           usleep

$ ls /sbin
acpid              fdisk              ifconfig           ipneigh            makedevs           nameif             setconsole         tc
adjtimex           findfs             ifdown             iproute            mdev               pivot_root         slattach           tunctl
arp                freeramdisk        ifenslave          iprule             mkdosfs            poweroff           start-stop-daemon  udhcpc
blkid              fsck.minix         ifup               iptunnel           mke2fs             raidautorun        sulogin            uevent
blockdev           fstrim             init               klogd              mkfs.minix         reboot             swapoff            vconfig
bootchartd         getty              insmod             loadkmap           mkfs.vfat          rmmod              swapon             watchdog
depmod             halt               ip                 logread            mkswap             route              switch_root        zcip
devmem             hdparm             ipaddr             losetup            modinfo            run-init           sysctl
fbsplash           hwclock            iplink             lsmod              modprobe           runlevel           syslogd
```
