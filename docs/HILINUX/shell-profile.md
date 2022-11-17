# shell添加profile

```sh
vi /root/.profile
source /root/.profile
```

以添加 `alias` 为例：

```sh
alias ll="ls -alFh"
```

添加环境变量、初始化指令同理。

也可以修改 `/etc/profile`，因为只有 `root` 用户，与修改 `/root/.profile` 是没有区别的。
