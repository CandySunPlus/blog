title: Docker 心得
date: 2015-08-11 19:29:25
tags: Docker
categories: Linux
thumbnailImage: http://7xl1ay.com1.z0.glb.clouddn.com/docker.jpg
---
Docker 的 `image` 存放位置通过修改服务的 `-g` 选项进行修改。

在 `ubuntu` 上修改 `/etc/default/docker.io`，内容为：

```bash
DOCKER_OPTS="-g /path/to/docker/you/want/put"
```

Docker 会记录 `image` 所有 `build` 的历史，所以 `build` 的过程尽量精简。
Docker 允许一个 `image` 的操作历史不可以大于 `127` 次。
Docker 在完成一个项目的 `image` 基础版本 `build` 后，为了去除 `build` 过程中的历史以及当前 `image` 依赖的基础 `image` 的历史，需要从可运行的 `container` 上 `docker export` 一份 `image` 的包，然后通过 `docker import` 进行导入，这样生成的新的 `image` 没有任何历史，可以作为项目的基础镜像。

导出过程：

```bash
$ docker export [container-id] |gzip > /path/to/file.tar.gz
```

导入过程：

```bash
$ cat /path/to/file.tar.gz | docker import - [image-name:image-tag]
```

用以上方式导出会丢弃历史记录以及 曾经 `build` 过程中的 `ENV`, 所以基于此镜像进行更新操作时需要重新定义 `ENV`。
项目镜像后续的更新最好始终基于此项目基础镜像，更新 `build` 的过程中打一个不同的 `tag` 以与 基础版本区分，这样能保证后续更新镜像的精简。
可以使用 `SSH` 等方法登入项目基础镜像进行修正。

修正后需要：

```bash
$ docker commit [container-id] [image-name:image-tag]
```

注意修改版本应当随时同步到远程。
运行失败的 `container` 及时进行删除
可以使用 `busybox` 来设置一个最基础的文件环境

建立方法：

```bash
$ docker run --name [volume-container-name] -v /path/you/want/create busybox:latest true
```

然后在别的 `container` 中可以使用这个最基本的文件系统， 多个使用此文件环境的 `container` 之间的修改是同步的。

使用过程：

```bash
$ docker run --volumes-from [volume-container-name] -i -t [image-name:image-tag] /bin/bash
```

此时指定挂载的 `container` 的目录就会出现在当前 `container` 相对应的位置, 你可以在这里进行任何操作。

但是我们所做的操作并不会更新到我们用 `busybox` 建立的 `container` 中，而且这份文件环境中的数据的生命周期 **不是** 在 这个 `busybox container` 被 `docker rm` 掉后就结束的，**他是在所有对他有引用的 `container` 都被 `docker rm` 后才会消失的。**

我们要通过以下办法进行数据的备份和还原。 备份的时候使用精简无 `entry` 的 `image` 来进行压缩。

这里使用 `busybox`：

```bash
$ docker run --volumes-from [volume-container-name] -v /host/path/to/put/backup/file:/backup busybox:latest tar cvf /backup/backup_file.tar /path/need/to/backup
```

这样就会把这份文件环境中的数据打一个 `tar` 的包备份到你指定的位置。

恢复时要先建立一个基础文件环境：

```bash
$ docker run --name [volume-container-name] -v /path/you/want/create busybox:latest true
```

然后同样使用没有 `entry` 的 `images` 建立 `container` 解压，这里同样使用 `busybox`：

```bash
$ docker run --volumes-from [volume-container-name] -v /host/path/to/backup/file:/backup busybox:latest tar xvf /backup/backup_file.tar
```

这样文件环境的内容就回来了，你可以在其他 `container` 中进行 `volumes-from` 来使用了。



