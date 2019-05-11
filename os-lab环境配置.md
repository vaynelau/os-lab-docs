# 操作系统实验环境配置

## 操作系统版本

```
Ubuntu 18.04.2 LTS
gcc (Ubuntu 7.3.0-27ubuntu1~18.04) 7.3.0
```

或

```
Ubuntu 16.04.6 LTS
gcc (Ubuntu 5.4.0-6ubuntu1~16.04.11) 5.4.0 20160609
```

## 安装交叉编译器

从以下地址下载：

```sh
wget http://ftp.denx.de/pub/eldk/4.1/mips-linux-x86/iso/mips-2007-01-21.iso
```

安装方式1：

```sh
# 安装32 位运行库（仅在64 位系统上需要执行此步骤）
# sudo apt install ia32-libs
sudo apt install lib32ncurses5 lib32z1

# 建立一个用于挂载ISO 镜像的目录
sudo mkdir /mnt/mipsiso

# 挂载iso文件
sudo mount -o loop mips-2007-01-21.iso /mnt/mipsiso

# 切换到/mnt/mipsiso 文件夹中
cd /mnt/mipsiso

# 运行安装脚本
sudo ./install -d /opt/eldk
```

第三部挂载iso文件时可能遇到如下错误：

```sh
$ sudo mount -o loop mips-2007-01-21.iso ./mipsiso 
mount: ./mipsiso: mount failed: Unknown error -1
```

可使用7z进行解压：

```sh
sudo apt install p7zip-full p7zip-rar # Ubuntu
7z x mips-2007-01-21.iso -omipsiso # -o指定路径
cd mipsiso
chmod a+x install
sudo ./install -d /opt/eldk
```

安装完成后执行`/opt/eldk/usr/bin/mips_4KC-gcc --version`得到如下输出说明安装成功。

```
mips_4KC-gcc (GCC) 4.0.0 (DENX ELDK 4.1 4.0.0)
Copyright (C) 2005 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

## 安装仿真器

首先下载了0.4.6版本的gxemul：

```sh
wget http://gavare.se/gxemul/src/gxemul-0.4.6.tar.gz
```

`make`时出现以下错误

```
cc -fstrict-aliasing -fomit-frame-pointer -fpeephole -O3 -DNDEBUG  src/*.o src/cpus/*.o src/debugger/*.o src/devices/*.o src/disk/*.o src/file/*.o src/machines/*.o src/native/*.o src/net/*.o src/promemul/*.o src/useremul/*.o  -lm  -o gxemul
src/devices/dev_sgi_ip32.o: In function `__cmsg_nxthdr':
dev_sgi_ip32.c:(.text+0x1d60): multiple definition of `__cmsg_nxthdr'
src/emul.o:emul.c:(.text+0x10): first defined here
src/devices/dev_sgi_ip32.o: In function `recv':
dev_sgi_ip32.c:(.text+0x1db0): multiple definition of `recv'
src/emul.o:emul.c:(.text+0x60): first defined here
src/devices/dev_sgi_ip32.o: In function `recvfrom':
dev_sgi_ip32.c:(.text+0x1dc0): multiple definition of `recvfrom'
src/emul.o:emul.c:(.text+0x70): first defined here
src/machines/machine_sgi.o: In function `__cmsg_nxthdr':
machine_sgi.c:(.text+0xaf0): multiple definition of `__cmsg_nxthdr'
src/emul.o:emul.c:(.text+0x10): first defined here
src/machines/machine_sgi.o: In function `recv':
machine_sgi.c:(.text+0xb40): multiple definition of `recv'
src/emul.o:emul.c:(.text+0x60): first defined here
src/machines/machine_sgi.o: In function `recvfrom':
machine_sgi.c:(.text+0xb50): multiple definition of `recvfrom'
src/emul.o:emul.c:(.text+0x70): first defined here
collect2: error: ld returned 1 exit status
Makefile:28: recipe for target 'build' failed
make: *** [build] Error 1
```

最后重新下载了最新的0.6.1版本的gxemul仿真器

```sh
wget http://gavare.se/gxemul/src/gxemul-0.6.1.tar.gz
```

解压并安装

```sh
tar -zxvf gxemul-0.6.1.tar.gz
cd gxemul-0.6.1/
./configure
make
sudo make install
# sudo cp gxemul /usr/local/bin
```

最后安装成功。

## 下载实验项目

```sh
git clone git@github.com:laudavid/BUAA_MIPS_OS.git
```

## 修改交叉编译器路径

```sh
cd BUAA_MIPS_OS
vim include.mk
```

将

```
CROSS_COMPILE := /OSLAB/compiler/usr/bin/mips_4KC-
```

修改为

```
CROSS_COMPILE := /opt/eldk/usr/bin/mips_4KC-
```

## 运行

```sh
gxemul -E oldtestmips -C R3000 -M 64 gxemul/vmlinux
```

或

```sh
gxemul -E oldtestmips -C R3000 -M 64 -d gxemul/fs.img gxemul/vmlinux
```

## 安装32位库

生成磁盘文件时，需要先生成32位的可执行文件fsformat，可能需要安装相关的32位库：

```sh
sudo apt update
sudo apt install libc6-dev-i386
```

