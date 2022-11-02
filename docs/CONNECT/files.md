# 板卡和电脑传输文件

## macOS安装ftp

```
brew unlink telnet # 先将本机原本的telnet取消链接
brew install inetutils # 再安装所有的网络工具 其中包含telnet和ftp
```

然后就可以使用命令行的ftp来传输了

## macOS安装可视化工具

App Store 搜索 FTPRush，下载安装。

打开后在右上角输入 host、user 即可连接，之后使用拖拽传输文件即可。注意使用刷新按钮使应用内呈现的目录与实际相符。
