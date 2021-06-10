# SSH 远程登录

## 口令登录

```shell
$ ssh root@104.168.204.234
```

如果你是第一次登录对方主机，系统会出现下面的提示：

```shell
The authenticity of host '104.168.204.234 (104.168.204.234)' can't be established.
ECDSA key fingerprint is SHA256:QVLCrR3bYQQ+ouBUeAYIdMAz8mGqNJu+BAE3c1AkvSg.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

输入 yes，然后输入正确密码就可以了，之后再登录，只需要输入正确的密码。

# SSH 远程操作

- 传输文件

```bash
scp 文件地址 root@ip:路径
```

- 传输文件夹
  scp -r 文件地址 root@ip:路径

# SSH 常用命令
