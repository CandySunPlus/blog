title: 透明穿过跳板机登录服务器
date: 2016-05-18 11:09:26
tags: SSH Linux
---

很多时候为了保证服务器的安全，一般不允许直接用外网进行 `SSH` 终端登录，大部分时候会有一个建立一个内网 `VPN` 或者跳板机，透过这一层网络环境连接目标终端。

如果是使用的跳板机，那么每次目标终端的登录和文件传输都会增加操作的复杂性，不过我们可以通过 `SSH` 的 `-W [host:port]` 命令进行代理。

<!-- more -->

首先我们操作本地的 `SSH` 配置文件， 文件位置如下：

```
~/.ssh/config
```

增加跳板机的配置：

```ssh
Host Proxy
​    HostName <跳板机IP>
    User <跳板机用户> 
```

再在目标终端下配置 `ProxyCommand` ：

```ssh
Host Target
    HostName <目标终端内网IP>
    User <目标终端用户>
    ProxyCommand ssh Proxy -W %h:%p
```

这样我们在连接目标终端的时候， 直接执行如下：

```shell
$ ssh Target
```

就可以直接穿过跳板连接目标终端了。 同样，使用 `SCP` 传输文件也变得和没有跳板一样方便了：

```shell
$ scp <local file> Target:<remote path>
```

