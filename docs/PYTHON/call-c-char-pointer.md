# ctypes char数组

## 资料

Python 官方说了 `bytes` 可以自动转为 `char *`。而且注意转变后是可修改的，看下面的示例：

## 文件

**Makefile**:

```Makefile
CC=gcc
AR=ar

LIB_NAME=mylib

shared_library:
	$(CC) -shared -fpic -o lib${LIB_NAME}.so *.c

get_lib: shared_library
	rm -rf lib/
	mkdir -p lib
	cp *.so lib/
	make clean

clean:
	rm -rf *.o *.so
```

**main.py**:

```py
import ctypes

mylib = ctypes.cdll.LoadLibrary("./lib/libmylib.so")
length = 5
data = b"\x01" * (length + 2)
mylib.ModifyCharArray(data, length)
print(data)
```

**lib.c**:

```c
int ModifyCharArray(char* array, int length) {
  for (int i = 0; i < length; i++) {
    array[i] += 1;
  }
}
```
