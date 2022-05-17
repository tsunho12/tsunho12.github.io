---
title: Orange's 一个操作系统的实现
tags:
  - Orange‘S
categories:
  - OS
abbrlink: e7369fd6
date: 2022-04-20 14:51:12
---
## 环境搭建
书里给了两种环境，Windows和Linux，平台可以根据自己喜好，这里给出基于 macOS Monterey 的环境配置方式

本文默认你电脑上已经安装`brew`，如果没有安装，请自行百度安装。

<!-- more -->

### 首先是安装 Bochs

`brew install bochs`

### 安装 NASM

`brew install nasm`

可以使用 `bochs --version` 与 `nasm --version` 来查看是否安装成功

至此,我们的操作系统开发环境已经搭建好了.

## 最小的操作系统

操作系统代码boot.asm编译执行过程如下
```x86asm
	org	07c00h ; 告诉编译器程序加载到7c00处
	mov	ax,cs ;
	mov	ds, ax ;
	mov	es, ax ;
	call	DispStr ; 调用显示字符串例程
	jmp $ ; 无限循环
DispStr:
	mov	ax, BootMessage ;
	mov	bp, ax ; ES:BP = 串长度
	mov	cx, 16 ; CX = 串长度
	mov	ax, 01301h ; AH = 13, AL = 01h
	mov	bx, 000ch ; 页号为0(BH = 0)黑底红字
(BL = 0Ch, 高亮)
	mov dl, 0
	int 10h ; 10h 号中断
	ret
BootMessage:
mov dl, 0
int 10h
ret
BootMessage:	db "Hello, OS World!"
times	510-($-$$)	db	0 ; 填充剩下的空间, 使生成的二进制代码恰好512字节
dw	0xaa55; 结束标志
```
**a. 编译boot.asm**
`nasm boot.asm -o boot.bin`

**b.创建虚拟软盘**
输入命令`bximage`(bochs自带),按照默认配置回车，创建名为1.44M的后缀名`.img`的虚拟软盘

**c.将引导扇区boot.bin写入刚刚创建的虚拟软盘**
`.img`的名称需要根据你电脑生成的进行修改，默认为`a.img`，然后执行
`dd if=boot.bin of=a.img bs=512 count=1 conv=notrunc`

**d.创建bochsrc文件**
在命令行输入`vim bochsrc.disk`，将一下内容复制进去
```bash
# 设置虚拟机内存为32MB                                                                                                                                                                                            
megs: 32

# 设置BIOS镜像
romimage: file=$BXSHARE/BIOS-bochs-latest 

# 设置VGA BIOS镜像
vgaromimage: file=$BXSHARE/VGABIOS-lgpl-latest

# 设置从硬盘启动
boot: disk

# 设置日志文件
log: bochsout.txt

# 关闭鼠标
mouse: enabled=0

# 打开键盘
keyboard: type=mf, serial_delay=250

# 设置硬盘
ata0: enabled=1, ioaddr1=0x1f0, ioaddr2=0x3f0, irq=14

ata0-master: type=disk, path="a.img", mode=flat

# 显示GUI
display_library: sdl2
```

 将a.img和bochsrc放在相同目录,进入此目录,输入`bochs -f bochsrc.disk`即可

此时窗口会显示，按回车继续，即可。

![bochs](https://cdn.jsdelivr.net/gh/tsunho12/photos_library/img/20220420145519.png)

之后，再按`c`继续执行，即可在屏幕上看到输出的内容

![terminal](https://cdn.jsdelivr.net/gh/tsunho12/photos_library/img/20220420145545.png)

以上是使用 Bochs 运行的方法，接下来还可以使用另一款模拟器 QEMU 来达到同样的效果

在命令行输出`qemu-system-i386  -hda a.img -boot a -m 64 -localtime`，即可看到显示的效果

![hello, os world!](https://cdn.jsdelivr.net/gh/tsunho12/photos_library/img/20220420145603.png)
