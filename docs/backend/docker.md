# Docker

## 一、Docker 入门

> [Docker 是什么，有什么用？](https://zhuanlan.zhihu.com/p/187505981)

### 1. 开始

#### 下载 docker

apt 升级

```bash
sudo apt-get update
```

添加相关软件包

```bash
sudo apt-get install \ apt-gransport-https \ ca-certificates \ curl \ software-properties-common
```

下载软件包的合法性，需要添加软件源的 GPG 密钥  
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -

source.list 中添加 Docker 软件源

```bash
sudo add-apt-repository \"deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu \$(lsb_release -cs) \stable"
```

安装 Docker CE

```bash
sudo apt-get update
sudo apt-get install docker-ce
```

启动 Docker CE(添加 docker，启动 docker)

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

建立 docker 用户组

```bash
sudo groupadd docker
sudo usermod -aG docker $USER
```

Helloworld 测试

```bash
docker run hello-world
```

<hr/>

#### 运行一个 docker 容器

使用以下命令，可以在系统中运行一个镜像名为`docker/getting-started`的`Docker`容器

```bash
docker run -d -p 80:80 docker/getting-started
```

- `-d` 将`container`在后台独立运行
- `-p 80:80` 将主机的 80 端口映射到`container`的 80 端口
- `docker/getting-started` 所使用的`docker`仓库名称，如果本地没有这个仓库，则先去拉去，再运行
  ::: tip
  多个参数可以简写：
  docker run -dp 80:80 docker/getting-started
  :::

  #### 容器是什么？

  容器可以看作是一个和主机所有进程都不相关的被隔离的进程。

  #### 镜像是什么？

  镜像给容器提供了一个独立的文件系统，包含了容器所需要的所有配置，例如环境变量、要运行的默认命令以及其他的元数据；也包含了应用运行所需要的所有依赖、配置、脚本等，使应用在容器中可以完全运行。

  ### 2. 创建一个简单的应用

  #### 从 github 下载示例，app 文件夹

  [点击下载](https://github.com/docker/getting-started/tree/master/app)
