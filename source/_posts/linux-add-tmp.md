title: Linux 增加TMP 分区大小
date: 2015-08-11 19:50:37
tags: Linux
categories: Linux
---
**以下操作全部是在 `su` 权限下进行的。**
<!-- more -->

停止使用tmp文件系统的程序。

备份tmp：

```bash
$ cp -prf /tmp /tmp.bak
```

创建2G的文件：

```bash
$ fallocate -l 2G /opt/tmpdisk
```

格式化: 

```bash
$ mkfs.ext4 /opt/tmpdisk
```

检查文件：

```bash
$ file /opt/tmpdisk
```

卸载tmp：

```bash
$ umount /tmp
```

挂载tmp：

```bash
$ mount -o loop,nosuid,rw /opt/tmpdisk /tmp
```

修复权限：

```bash
$ install -d -m 1777 /tmp
```

增加fstab：

```bash
$ /opt/tmpdisk /tmp ext4 loop,nosuid,rw 0 0
```

挂载所有：

```bash
$ mount -a
```

接下来就可以通过 `df -h` 来查看一下操作的结果了。

