# 编译Python3.10.8(含zlib/pip)（已放弃）

参考 编译带有zlib的Python3.10.8 ，在Python编译脚本中的 `./configure` 一行最后加入 `--with-ensurepip=install`。

安装好之后执行 `python3 -m ensurepip --default-pip`。

可以看到 pip 已经安装成功：

```
$ ll bin
total 24M
drwxr-xr-x    2 1000     1000        4.0K Jan  1 00:21 ./
drwxrwxr-x    6 1000     1000        4.0K Jan  1 00:19 ../
lrwxrwxrwx    1 root     root           9 Jan  1 00:19 2to3 -> 2to3-3.10*
-rwxrwxr-x    1 1000     1000         130 Nov 16  2022 2to3-3.10*
lrwxrwxrwx    1 root     root           8 Jan  1 00:19 idle3 -> idle3.10*
-rwxrwxr-x    1 1000     1000         128 Nov 16  2022 idle3.10*
-rwxr-xr-x    1 root     root         259 Jan  1 00:21 pip*
-rwxr-xr-x    1 root     root         259 Jan  1 00:21 pip3*
-rwxr-xr-x    1 root     root         259 Jan  1 00:21 pip3.10*
lrwxrwxrwx    1 root     root           9 Jan  1 00:19 pydoc3 -> pydoc3.10*
-rwxrwxr-x    1 1000     1000         113 Nov 16  2022 pydoc3.10*
lrwxrwxrwx    1 root     root          10 Jan  1 00:19 python3 -> python3.10*
lrwxrwxrwx    1 root     root          17 Jan  1 00:19 python3-config -> python3.10-config*
-rwxr-xr-x    1 1000     1000       24.2M Nov 16  2022 python3.10*
-rwxr-xr-x    1 1000     1000        3.1K Nov 16  2022 python3.10-config*
```

## 设置环境变量

将 `pip` 和 `pip3` 设置 alias 或者直接将所在目录加入环境变量。

## 离线安装Python第三方包

### 查看目标电脑架构

以 numpy 为例：

```
$ python
>>> import distutils
>>> distutils.util.get_platform()
'linux-aarch64'
```

### 下载安装包

在有网络的电脑上：

```
cd /Users/yangxijie/Desktop/
mkdir numpy
pip download --python-version 310 --platform linux-aarch64 numpy -d "/Users/yangxijie/Desktop/numpy"
tar cvfz numpy.tgz numpy
```

### 安装

将 numpy.tgz 拷贝到板子上

```
tar xvfz numpy.tgz
cd numpy
pip install numpy -f ./ --no-index
```

```
~/yxj/SOFTWARE/numpy # pip install numpy -f ./ --no-i
ndex
Looking in links: ./
Processing ./numpy-1.23.4.tar.gz
  Installing build dependencies ... error
  error: subprocess-exited-with-error

  × pip subprocess to install build dependencies did not run successfully.
  │ exit code: 1
  ╰─> [4 lines of output]
      Looking in links: ./
      Ignoring packaging: markers 'platform_machine == "arm64"' don't match your environment
      ERROR: Could not find a version that satisfies the requirement setuptools==59.2.0 (from versions: none)
      ERROR: No matching distribution found for setuptools==59.2.0
      [end of output]

  note: This error originates from a subprocess, and is likely not a problem with pip.
error: subprocess-exited-with-error

× pip subprocess to install build dependencies did not run successfully.
│ exit code: 1
╰─> See above for output.

note: This error originates from a subprocess, and is likely not a problem with pip.
```

## References

- https://stackoverflow.com/questions/31273157/how-to-install-pip-on-compiled-from-source-python
- https://stackoverflow.com/questions/36725843/installing-python-packages-without-internet-and-using-source-code-as-tar-gz-and
- https://pip.pypa.io/en/stable/cli/pip_download/
- https://stackoverflow.com/questions/49672621/what-are-the-valid-values-for-platform-abi-and-implementation-for-pip-do
